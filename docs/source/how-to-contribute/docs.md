# 如何贡献文档教程

openEuler ROS在线文档（本文档）存储在ROS sig的门户仓库兼文档仓库[openEuler ROS](https://gitee.com/openeuler/ros)，使用Sphinx来构建文档，源文件存放于docs目录下。

## 1. Fork ros仓库并下载

将https://gitee.com/openeuler/ros仓库fork到自己名下，然后git clone下载自己名下的仓库。

## 2.本地构建

本地构建前可先参考docs/requirements.txt中的内容安装依赖项。

```
pip install sphinx recommonmark sphinx_rtd_theme sphinx_markdown_tables
```
进入`docs` 目录，第一次编译前需执行命令`sphinx-quickstart`

接着在 `docs` 目录下编译文档：

> ```
> make html
> ```

编译成功后，可在`docs` 目录下看到build目录，打开里面生成的*html格式的文件即可在浏览器看到静态网页文档。

每次提交或者修改文档时，应现在本地构建出静态文档查看自己新加的文档是否存在问题。

确认无误后可提交PR到https://gitee.com/openeuler/ros仓库。

## 3.其他说明

- 新加文档后记得在docs/source/index.rst中添加文档索引
- 本仓库已配置Sphinx markdown，也可使用md文件编写教程
- 图片上传到各个子目录的image文件夹
- 如有参考其他教程，需贴出参考文献或链接
