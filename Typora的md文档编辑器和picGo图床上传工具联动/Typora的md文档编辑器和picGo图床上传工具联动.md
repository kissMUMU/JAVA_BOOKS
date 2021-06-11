# Typora的md文档编辑器和picGo图床上传工具联动



什么是Typora？

 > Typora是一个所见即所得的Markdown格式文本编辑器，支持Windows、macOS和GNU/Linux操作系统，包括对GitHub Flavored Markdown扩展格式的支持、拼写检查、自定义CSS样式、数学公式渲染（通过MathJax）等特性。

如何下载Typora?

> [官网地址](https://typora.io/)

官网下载地址截图：

> ![image-20201208122345690](Typora%E7%9A%84md%E6%96%87%E6%A1%A3%E7%BC%96%E8%BE%91%E5%99%A8%E5%92%8CpicGo%E5%9B%BE%E5%BA%8A%E4%B8%8A%E4%BC%A0%E5%B7%A5%E5%85%B7%E8%81%94%E5%8A%A8.assets/image-20201208122345690.png)



我当前使用typora版本截图：

> ![image-20201208122616748](https://xxxa.oss-cn-shanghai.aliyuncs.com/img/image-20201208122616748.png)



设置界面截图：

> ![image-20201208122852184](https://xxxa.oss-cn-shanghai.aliyuncs.com/img/image-20201208122852184.png)
>
> 注意：老版本typora设置图像无上传功能。
>
> 按截图所示配置,并在上传服务设置处选中picGo。 图所示绿色选框暂时不进行验证图片上传功能，待配置picGo后在点击。





什么是picGo？

> 一个用于快速上传图片并获取图片 URL 链接的工具

官网地址:

> [picGo](https://molunerfinn.com/PicGo/)

github地址

> [下载地址](https://github.com/Molunerfinn/picgo/releases)



配置aliyun-oss截图：

> ![image-20201208123746004](Typora%E7%9A%84md%E6%96%87%E6%A1%A3%E7%BC%96%E8%BE%91%E5%99%A8%E5%92%8CpicGo%E5%9B%BE%E5%BA%8A%E4%B8%8A%E4%BC%A0%E5%B7%A5%E5%85%B7%E8%81%94%E5%8A%A8.assets/image-20201208123746004.png)
>
> 设定keyId:  阿里云oss申请的acessKeyId
>
> 设定keySecret:  阿里云oss申请的acessKeySecret
>
> 设定存储空间名：阿里云oss创建的Bucket名
>
> 确认储存区域：根据Bucket名点击概览查看当前oss存储区域 (可以根据oss对外访问的url地址中获取储存区域)
>
> 指定储存路径：在当前Bucket下已什么路径保存上传文件



阿里云accessKey申请 ：

> ![image-20201208124006947](Typora%E7%9A%84md%E6%96%87%E6%A1%A3%E7%BC%96%E8%BE%91%E5%99%A8%E5%92%8CpicGo%E5%9B%BE%E5%BA%8A%E4%B8%8A%E4%BC%A0%E5%B7%A5%E5%85%B7%E8%81%94%E5%8A%A8.assets/image-20201208124006947.png)





