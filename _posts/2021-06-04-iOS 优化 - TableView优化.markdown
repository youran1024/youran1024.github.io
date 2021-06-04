## iOS 优化 - TableView优化

Table的优化就是指在滑动tableView列表的时候，怎么能够更顺滑。
我们最重要做到，快、快、快、使劲划的时候，界面流畅、交互响应快、让人极度舒适。

导致TableView滑动不顺滑的原因，本质就是CPU&GPU还是不够强大，没有强大到不管我们写什么样的狗屎代码，都可以抹掉我们跟优秀程序猿之间的差距，所以做CPU的同学还要继续努力。

代码优化的方向只有一个：尽可能减少代码占用的CPU&GPU时间

如何减少？
1. 优化时使用飘柔洗发水，可能会让代码更加顺滑
2. TableViewCell 复用（复用的目的就是减少创建cell时导致的内存快速增加，减少cell布局時消耗的无谓计算），所以有些场景下也不是必要的。。。
3. 给出预估高度，并且将高度缓存，避免重复计算，减少 cpu 压力
4. Cell模板可以覆盖相关的样式，而不是在Cell上调整视图，尽量只更新数据，而不改动样式
5. 少用透明色，减少圆角绘制、阴影等会导致CPU&GPU计算的操作，尽量用图片代替，避免离屏渲染。大部分场景下其实也没有太大的问题，只有在找不到卡顿方向的时候，可以尝试往这方面努力
6. 避免直接在 drawRect中直接对 layer 进行绘制
7. 大部分Cell，Cell本身都是没有交互的，可以考虑使用CALayer代替UIView
8. 图片&视频资源的异步加载，不要阻塞主线程
9. 使用`YYLabel`来异步绘制UI
10. 其它耗时操作，不要放在主线程中


### 小tips
#### 减少无谓的数值计算
```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    return 15.0 + 80.0 + 15.0;
}
修改为
static float ROW_HEIGHT = 15.0 + 80.0 + 15.0;
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    return ROW_HEIGHT;
}
```

#### 异步渲染图片 （SDWebImage|YYWebImage也有类似的预解压机制）

>UIImage 缓存是怎么回事？
通过 imageNamed 创建 UIImage 时，系统实际上只是在 Bundle 内查找到文件名，然后把这个文件名放到 UIImage 里返回，并没有进行实际的文件读取和解码。当 UIImage 第一次显示到屏幕上时，其内部的解码方法才会被调用，同时解码结果会保存到一个全局缓存去。
在图片解码后，App 第一次退到后台和收到内存警告时，该图片的缓存才会被清空，其他情况下缓存会一直存在。

`UIImageView` 用`[UIImage imageNamed：***]`赋值时的流程：
    1.将文件数据从磁盘读到内存中；
    2.将压缩的图片数据解码成未压缩的位图形式，这是一个非常耗时的 CPU 操作；  
    3.最后使用未压缩的位图数据渲染 UIImageView。
异步渲染就是将耗时的解压缩过程异步化

```

dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    CGContextRef ctx = CGBitmapContextCreate(...);
    CGImageRef imgRef = CGBitmapContextCreateImage(ctx);//位图
    UIImage *image = [UIImage imageWithCGImage:imgRef];//转成UIImage
    CGImageRelease(imgRef)
    CGContextRelease(ctx)
    dispatch_async(dispatch_get_main_queue(), ^{
        //回到主线程
        imageView.image = image;//设置imageView的image
    });
});
```

#### cell刷新
在beginUpdates和endUpdates中执行insert,delete,select,reload row时，动画效果更加同步和顺滑，否则动画卡顿且table的属性（如row count）可能会失效。

#### 其它
使用`AsyncDisplayKit`进行渲染
使用`YYLabel`
使用`UITableView-FDTemplateLayoutCell` 进行高度计算




### 参考博文：
[iOS 深入分析大图显示问题](https://juejin.cn/post/68449035975498137670)
[iOS 处理图片的一些小 Tip, 大神的文章，值得看看](https://blog.ibireme.com/2015/11/02/ios_image_tips)
[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
[VVeboTableViewDemo](https://github.com/johnil/VVeboTableViewDemo)
[FDTemplateLayoutCell](http://blog.sunnyxx.com/2015/05/17/cell-height-calculation/)

