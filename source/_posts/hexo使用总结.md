---
title: hexo使用总结
date: 2019-04-24 08:36:57
tags: tool
---

#### 更新主页文章
- 更新文章分为四步：
    - 创建文章：命令：**hexo new** "你的文章名称"。该命令会在你hexo项目的source/_post目录下，生成"你的文章名称".md的markdown文件。
    - 编辑文件。利用markdown工具，编辑source/_post/"你的文章名称".md的文件
    - 预览文章。命令：**hexo generate** 和 **hexo server**。先利用hexo generate生成文章，再利用hexo server命令启动本地服务器。在浏览器中输入本地博客地址，可以查看文章的效果。
    - 部署发布文章。命令：**hexo g -d**。当文章编写完成之后，运行该命令，就会生成并发布文章到你的个人博客网站。
