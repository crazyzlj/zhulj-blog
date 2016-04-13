---
layout: post
title: "Automatic Documentation Publishing with GitHub Pages, doxggen, and TravisCI"
category: [Others]
tag: [Github,mannual]
date: 2016-04-12 20:00:00
comments: true
---

# 如何利用Github Pages, doxygen和Travis CI构建自动更新文档系统
-------------

## 参考资料：

+ 1.[Creating Project Pages manually by Github](https://help.github.com/articles/creating-project-pages-manually/)
+ 2.[Automatic Documentation Publishing with GitHub and TravisCI](http://blog.gockelhut.com/2014/09/automatic-documentation-publishing-with.html "Automatic Documentation Publishing with GitHub and TravisCI")


# 1 手工创建Github Pages

```
# 克隆一个纯净仓库
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

# 2 为Travis CI创建SSH key

```
cd repository
mkdir config
ssh-keygen -t rsa -C "youremail@example.com" -f config/travisci_rsa
# travisci_rsa和travisci_rsa.pub会生成在当前仓库目录/config下
```

此时，可将私有密钥`travisci_rsa`上传至Github但是，这样会带来一点安全隐患，而我们希望我们的私有密钥`travisci_rsa`放到Travis CI上而不被其他人看到。很显然，我们不能直接将其上传至公开仓库。

幸好，Travis CI提供了加密文件的方案，即Travis CI CLI，可从Ruby Gem安装，Windows下请首先安装[Ruby](http://rubyinstaller.org/downloads)，替换掉官方的sources，参考[这篇博客](http://zhulj.net/others/2016/03/17/Github-jekyll-blog.html)。

```
gem sources --add http://gems.ruby-china.org/ --remove https://rubygems.org/
gem install travis
travis login
# 然后输入Github的用户名、密码进行验证
# 创建.travis.yml
echo "before_install:" > .travis.yml
# 然后便可以加密文件了
travis encrypt-file config/travisci_rsa --add
# Output:
# encrypting travisci_rsa for seims/SEIMS
# storing result as travisci_rsa.enc
# storing secure env variables for decryption
# Make sure to add super_secret.txt.enc to the git repository.
# Make sure not to add super_secret.txt to the git repository.
# Commit all changes to your .travis.yml.

# 将travisci_rsa.enc上传至Github，而不要讲travisci_rsa上传，可以将travisci_rsa删掉
rm config/travisci_rsa
mv travisci_rsa.enc config

# 将以下两行添加至.travis.yml
- chmod 0600 config/travisci_rsa
- cp config/travisci_rsa ~/.ssh/id_rsa
```

# 3 发布文档

一切准备就绪，可以利用Doxygen发布文档啦。

TO BE CONTINUE



