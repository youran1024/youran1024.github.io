## 先检查下你使用的环境
```shell
# 当前使用的shell
echo $SHELL

# 系统支持的shell
cat /etc/shells

# 推荐 ZSH + iterm2
https://segmentfault.com/a/1190000014992947

# 如果是bash，则修改
vi ~/.bash_profile

# 如果是zsh，则修改
vi ~/.zshrc

```
## $ANDROID_HOME
#### 1. 查看ANDROID_HOME的存储位置
![image.png](https://upload-images.jianshu.io/upload_images/299790-a438b61d20ed324f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 设置步骤
```shell
# 1. 打开配置文件
vi ~/.bash_profile

# 2. 粘贴如下代码，注意修改path路径为上文中查到的路径
export ANDROID_HOME=/Users/{Your User}/Library/Android/sdk
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools

# 3. 使配置生效
source ~/.bash_profile

# 4. 检查是否已生效
echo $ANDROID_HOME

# 4. 结果输出
/Users/{Your User}}/Library/Android/sdk
```
---
## $JAVA_HOME
```
# 1. 打开配置文件
vi ~/.bash_profile

# 2. 粘贴如下代码，注意修改path路径为上文中查到的路径
export JAVA_HOME=/usr/libexec/java_home
export PATH=${PATH}:${JAVA_HOME}/bin

# 3. 使配置生效
source ~/.bash_profile

# 4. 检查是否已生效
echo $JAVA_HOME

# 5. 结果输出
/Library/Java/JavaVirtualMachines/jdk-13.jdk/Contents/Home
```

# 注意
> 如果是 zsh，需要修改的是 ~/.zshrc
```
# export MANPATH="/usr/local/man:$MANPATH"

export ANDROID_HOME=/Users/{your name}/android-sdk-macosx
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools


export JAVA_HOME=$(/usr/libexec/java_home)
export PATH=$JAVA_HOME/bin:$PATH
export CLASS_PATH=$JAVA_HOME/lib
```

MacOS环境变量的加载顺序为：
/etc/profile /etc/paths 
~/.bash_profile
~/.bash_login
~/.profile
~/.bashrc

立即让配置生效的方式`> source ~/.bash_profile`


# 后语：
如果修改了配置zshrc | bash_profile后， 每次重启终端都不生效，问题就是你当前使用的shell和你修改的配置文件不匹配，请参考步骤一修改。

祝你好运~
