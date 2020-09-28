---
	title: hexo添加图片，网页首页正常浏览，结合typora，实现复制图片，自动插入
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-28 18:12:23
password:
summary:
tags:
categories:
---



在根目录下配置文件_config.yml 中有 post_asset_folder:false改为true。这样在建立文件时，Hexo会自动建立一个与文章同名的文件夹，这样就可以把与该文章相关的所有资源（图片）都放到那个文件夹里方便后面引用。如这里我放了一张test.jpg的图片。
git bash安装插件：npm install https://github.com/7ym0n/hexo-asset-image --save（这是个修改过的插件，经测试无问题），使用这个插件来引入图片



设置typora，偏好设置->图片设置:



![image-20200428182128636](hexo%E6%B7%BB%E5%8A%A0%E5%9B%BE%E7%89%87%EF%BC%8C%E7%BD%91%E9%A1%B5%E9%A6%96%E9%A1%B5%E6%AD%A3%E5%B8%B8%E6%B5%8F%E8%A7%88%EF%BC%8C%E7%BB%93%E5%90%88typora%EF%BC%8C%E5%AE%9E%E7%8E%B0%E5%A4%8D%E5%88%B6%E5%9B%BE%E7%89%87%EF%BC%8C%E8%87%AA%E5%8A%A8%E6%8F%92%E5%85%A5.assets/image-20200428182128636.png)



![image-20200428182128636](hexo%E6%B7%BB%E5%8A%A0%E5%9B%BE%E7%89%87%EF%BC%8C%E7%BD%91%E9%A1%B5%E9%A6%96%E9%A1%B5%E6%AD%A3%E5%B8%B8%E6%B5%8F%E8%A7%88%EF%BC%8C%E7%BB%93%E5%90%88typora%EF%BC%8C%E5%AE%9E%E7%8E%B0%E5%A4%8D%E5%88%B6%E5%9B%BE%E7%89%87%EF%BC%8C%E8%87%AA%E5%8A%A8%E6%8F%92%E5%85%A5/image-20200428182128636.png)

{% asset_img %E6%88%AA%E5%B1%8F2020-04-28%20%E4%B8%8B%E5%8D%885.37.49.png This is an image %}



![alt](hexo%E6%B7%BB%E5%8A%A0%E5%9B%BE%E7%89%87%EF%BC%8C%E7%BD%91%E9%A1%B5%E9%A6%96%E9%A1%B5%E6%AD%A3%E5%B8%B8%E6%B5%8F%E8%A7%88%EF%BC%8C%E7%BB%93%E5%90%88typora%EF%BC%8C%E5%AE%9E%E7%8E%B0%E5%A4%8D%E5%88%B6%E5%9B%BE%E7%89%87%EF%BC%8C%E8%87%AA%E5%8A%A8%E6%8F%92%E5%85%A5/image-20200428182128636.png)



**{% asset_img %E6%88%AA%E5%B1%8F2020-04-28%20%E4%B8%8B%E5%8D%885.37.49.png This is an example image %}**





代码两种都可以(如果当时不显示记得执行cl和g，稍等一会就可以了)：

- **`{% asset_img %E6%88%AA%E5%B1%8F2020-04-28%20%E4%B8%8B%E5%8D%885.37.49.png This is an example image %}**`
- `![image-20200428182128636](hexo%E6%B7%BB%E5%8A%A0%E5%9B%BE%E7%89%87%EF%BC%8C%E7%BD%91%E9%A1%B5%E9%A6%96%E9%A1%B5%E6%AD%A3%E5%B8%B8%E6%B5%8F%E8%A7%88%EF%BC%8C%E7%BB%93%E5%90%88typora%EF%BC%8C%E5%AE%9E%E7%8E%B0%E5%A4%8D%E5%88%B6%E5%9B%BE%E7%89%87%EF%BC%8C%E8%87%AA%E5%8A%A8%E6%8F%92%E5%85%A5/image-20200428182128636.png)`

