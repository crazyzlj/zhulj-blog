---
layout: post
title: "Automatic Documentation Publishing with GitHub Pages and TravisCI"
category: [Others]
tag: [Github,mannual]
date: 2016-04-12 20:00:00
comments: true
---

# 如何利用Github Pages和Travis CI构建自动更新文档系统
-------------

## 参考资料：

+ 1.[Creating Project Pages manually by Github](https://help.github.com/articles/creating-project-pages-manually/)
+ 2.[Automatic Documentation Publishing with GitHub and TravisCI](http://blog.gockelhut.com/2014/09/automatic-documentation-publishing-with.html "Automatic Documentation Publishing with GitHub and TravisCI")


# 1 手工创建Github Pages

```
# 克隆一个纯净库
git clone github.com/user/repository.git
# 创建gh-pages分支
cd repository
git checkout --orphan gh-pages
# 这个gh-pages分支不会影响其他分支，因为它是orphan！
# 删除所有库内文件
git rm -rf .
# 创建一个html并push到gh-pages分支
echo "My Page" > index.html
git add index.html
git commit -a -m "First pages commit"
git push origin gh-pages
```

这样，就可以通过http(s)://<username>.github.io/<projectname>访问项目主页了，比如[http://seims.github.io/SEIMS/](http://seims.github.io/SEIMS/)，注意，repository大小写敏感！

# 2 