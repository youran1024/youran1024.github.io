## 背景
1. mac 电脑
2. 希望将python文件打包成可执行文件

## 问题
1. 执行`pyinstaller -F **.py`后
2. ./dist/*** 可执行文件
报如下错误：
```shell
Traceback (most recent call last):
  File "site-packages/PyInstaller/loader/rthooks/pyi_rth__tkinter.py", line 30, in <module>
FileNotFoundError: Tcl data directory "/var/folders/xf/hms3dl112jxgjllkrjsysy05nt2l4d/T/_MEIOsITDy/tcl" not found.
[56538] Failed to execute script pyi_rth__tkinter

```

## 解决方案：
```shell
pyinstaller --onefile --add-binary='/System/Library/Frameworks/Tk.framework/Tk':'tk' --add-binary='/System/Library/Frameworks/Tcl.framework/Tcl':'tcl'
```

## 总结
1. 希望能帮到你
2. 祝你好运
