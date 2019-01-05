# MarkdownPicPicker

[![Documentation Status](https://readthedocs.org/projects/markdownpicpicker/badge/?version=latest)](http://markdownpicpicker.readthedocs.io/en/latest/?badge=latest)
![](https://travis-ci.org/kingname/MarkdownPicPicker.svg?branch=master)
[![Say Thanks!](https://img.shields.io/badge/Say%20Thanks-!-1EAEDB.svg)](https://saythanks.io/to/kingname)

## 注意

七牛云现在上线了一个新功能，强迫你在功能上线30天内必需绑定自定义域名，否则会删除你的临时域名，导致你的所有图片全部不可访问。我已经放弃使用七牛云了。现在推荐使用腾讯云，免费10G流量，不需要备案就能使用CDN，我会尽快上线支持腾讯云的上传组件。

## 项目介绍

MarkdownPicPicker 是一个Markdown写作辅助工具。它能将剪贴板中的图片上传到网络图床中，并将markdown格式的图片链接(\!\[\]\(<图片地址>\))复制到剪贴板中。

![MarkdownPicPicker演示](https://raw.githubusercontent.com/kingname/MarkdownPicPicker/master/screenshots/MarkdownPicPickerPrew.gif)

## 功能介绍

第1.1.0版有以下功能：

1. 图床支持七牛云与SM.MS, 默认使用SM.MS
2. 开箱即用，不需要配置
3. 通过配置实现自定义图床
4. 支持Mac OS
5. 将图片保存在本地
6. 图片上传成功后将Markdown格式的图片地址保存到剪贴板中
7. Uploader扩展性

## 使用方法

### Windows

1. 下载最新版程序：[https://github.com/kingname/MarkdownPicPicker/releases/download/v1.0.0/MarkdownPicPicker_v1.0.0.zip](https://github.com/kingname/MarkdownPicPicker/releases/download/v1.0.0/MarkdownPicPicker_v1.0.0.zip)
2. 复制图片到剪贴板
3. 双击运行markdownpicpicker.exe

### Mac OS
0. 安装pngpaste: `brew install pngpaste`
1. 下载并解压MarkdownPicPicker：[https://github.com/kingname/MarkdownPicPicker/releases/download/v1.0.0/macOS_MarkdownPicPicker.zip](https://github.com/kingname/MarkdownPicPicker/releases/download/v1.0.0/macOS_MarkdownPicPicker.zip)
2. 截图到剪贴板
3. 运行MarkdownPicPicker.app

## 配置
### 使用七牛云
在没有配置文件的情况下，MarkdownPicPicker默认使用的图床为SM.MS, 但是这个图床仅仅作为临时使用，可能会被墙，也不保证数据安全。如果你需要使用七牛云，请创建config文件夹，并将config.ini配置好再放进去。

### 配置

配置文件保存在`config/config.ini`文件中，其意义分别如下：
```ini
[basic]
picture_folder = pic #必需，用来在本地保存图片的副本，名字任意
picture_suffix = png #必需，图片后缀名，请使用png
# 图片上传插件的文件名，不带".py"
picture_host = QiniuUploader

[QiniuUploader]
access_key = Q6sS422O0fasfsadasdfahqasdftqvyQasdf5Zvzw
secret_key = 6QtAqqTxoSxZadffsdfasdfaaffasCmoOaB2aLObM
container_name = picturebed
url = http://7sbpmp.com1.z0.glb.clouddn.com/{}
```

注意：在实际使用中，请将上面的注释部分去掉，避免出现不必要的问题。

其中`access_key` 和 `secret_key` 可以在七牛云的控制面板中看到，如图：
![](http://7sbpmp.com1.z0.glb.clouddn.com/20160605083025.png) 
![](http://7sbpmp.com1.z0.glb.clouddn.com/2016-06-04-20-22-43.png) 

`container_name` 为下图所示内容：
![](http://7sbpmp.com1.z0.glb.clouddn.com/2016-06-04-20-24-40.png) 

###只复制图片链接

如果你希望只将图片的url复制到剪贴板中，而不是复制\!\[\]\(图片url\)， 你可以为markdownpicpicker.exe添加上 `-linkonly` 参数。在markdownpicpicker.exe所在目录打开cmd, 输入:
```
markdownpicpicker.exe -linkonly
```

建议大家使用AutoHotKey来启动程序。这样可以把整个流程缩短到2秒钟。AutoHotKey的配置示例如下图所示：

![](http://7sbpmp.com1.z0.glb.clouddn.com/2016-07-16-11-54-13.png) 

需要首先使用QQ截图或者其他截图工具将图片保存到剪贴板中，然后按下设定好的快捷键即可。Markdown格式的图片链接就已经保存到剪贴板中了。在需要使用的地方直接粘贴。

##开发
目前MarkdownPicPicker支持[七牛云](http://www.qiniu.com/)和[SM.MS](https://sm.ms/)两个图床,但是你可以非常方便的将自己的图床集成到MarkdownPicPicker。希望你能在集成了自己常用的图床以后，给我发一个PR，从而造福大家。

### 规则说明：

* 请将你的图床上传程序放在uploader文件夹下，文件名任意，例如`ExampleUploader.py`
* 程序的类名必需为Uploader
* 如果你的图床需要token等等一系列参数，请在__init__中设置config_info参数,你写在配置文件中的所有参数都将会通过config_info以字典的形式传进来。
```
def __init__(self, config_info=None)
```
* 程序必须有一个upload方法：
```
def upload(self, picture_path, link_only=False)
```
* 打开config/config.ini, 添加你的上传程序的相关信息，Section为你的上传程序的名字（不含.py）并设定[basic]下的picture_host为此Section例如：
```
[basic]
picture_folder = pic
picture_suffix = png
picture_host = ExampleUploader

[ExampleUploader]
token = 123456
url = http://xxx.xxx/upload
```

## 说明
如果你希望自己从源代码编译程序，请注意以下问题：

### Pillow bug修正
本程序Windows端使用了Pillow库中的 `ImageGrab.grabclipboard()` 方法来获取剪贴板中的数据，但是由于这个方法有一个bug, 导致可能会发生以下错误：
```
Unsupported BMP bitfields layout
```
这个问题从Pillow 2.8.0开始，一直到3.2.0都没有被官方解决。目前有一个间接的解决办法。
请打开Python安装目录下的\Lib\site-packages\PIL\BmpImagePlugin.py文件，将以下代码：

```
if file_info['bits'] in SUPPORTED:
    if file_info['bits'] == 32 and file_info['rgba_mask'] in SUPPORTED[file_info['bits']]:
        raw_mode = MASK_MODES[(file_info['bits'], file_info['rgba_mask'])]
        self.mode = "RGBA" if raw_mode in ("BGRA",) else self.mode
    elif file_info['bits'] in (24, 16) and file_info['rgb_mask'] in SUPPORTED[file_info['bits']]:
        raw_mode = MASK_MODES[(file_info['bits'], file_info['rgb_mask'])]
    else:
        raise IOError("Unsupported BMP bitfields layout")
else:
    raise IOError("Unsupported BMP bitfields layout")
```

修改为：
```
if file_info['bits'] in SUPPORTED:
    if file_info['bits'] == 32 and file_info['rgba_mask'] in SUPPORTED[file_info['bits']]:
        raw_mode = MASK_MODES[(file_info['bits'], file_info['rgba_mask'])]
        self.mode = "RGBA" if raw_mode in ("BGRA",) else self.mode
    elif file_info['bits'] in (24, 16) and file_info['rgb_mask'] in SUPPORTED[file_info['bits']]:
        raw_mode = MASK_MODES[(file_info['bits'], file_info['rgb_mask'])]
    '''新增内容开始'''
    elif file_info['bits'] == 32 and file_info['rgb_mask'] == (0xff0000, 0xff00, 0xff):
        pass
    '''新增内容结束'''
    else:
        raise IOError("Unsupported BMP bitfields layout")
else:
    raise IOError("Unsupported BMP bitfields layout")
```
就能解决本问题。

## TODO
* 支持更多的截图方式
* 不借助pngpaste实现获取剪贴板中的截图
* 支持更多的图床
* 窗口隐藏
* 适配Linux

## 感谢

* [赖信涛](https://github.com/laixintao)
* [Sean10](https://github.com/Sean10)
