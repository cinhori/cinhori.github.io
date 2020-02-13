---
layout: post
title: 利用GitHub Pages和Jekyll搭建博客
categories: GitHub
description: 利用GitHub Pages和Jekyll搭建博客
keywords: jekyll, GitHub
---

# 快速入门
## Jekyll
下面是Jekyll的官方介绍：
>Jekyll is a static site generator. You give it text written in your favorite markup language and it uses layouts to create a static website. You can tweak how you want the site URLs to look, what data gets displayed on the site, and more.
>Jekyll is written in Ruby. If you’re new to Ruby, this page is to help you get up to speed with some of the terminology.

## 关键字
**Gems**<br>
ruby工具集合
>A gem is code you can include in Ruby projects. It allows you to package up functionality and share it across other projects or with other people.

**Gemfile**<br>
类比makefile
>A Gemfile is a list of gems required for your site. 

语法：
```
source "https://rubygems.org"

gem "jekyll"

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
end
```

**Bundler**<br>
类比make工具
>Bundler installs the gems in your Gemfile. It’s not a requirement for you to use a Gemfile and bundler however it’s highly recommended as it ensures you’re running the same version of Jekyll and Jekyll plugins across different environments.
>`gem install bundler` installs Bundler. You only need to install it once — not every time you create a new Jekyll project. Here are some additional details:
If you’re using a `Gemfile` you would first run `bundle install` to install the gems, then `bundle exec jekyll serve` to build your site. This guarantees you’re using the gem versions set in the `Gemfile`. If you’re not using a `Gemfile` you can just run `jekyll serve`.

**_config.yml**
>Jekyll gives you a lot of flexibility to customize how it builds your site. These options can either be specified in a `_config.yml` or `_config.toml` file placed in your site’s root directory, or can be specified as flags for the `jekyll` executable in the terminal.

配置参数见[_config.yml配置](https://jekyllrb.com/docs/configuration/)

# 安装与配置
本博客使用[mzlogin](https://github.com/mzlogin/mzlogin.github.io)模板，感谢@mzlogin！  
环境：Win10  
## 博客初始化
>1. fork
>2. clone到本地
>3. 修改分支、`_config.yml`、删除_posts、_drafts、_wiki、images、关于内容

## 配置Jekyll环境
>1. Download and Install a Ruby+Devkit version from [RubyInstaller Downloads](https://rubyinstaller.org/downloads/) 
>2. Run the `ridk install` step on the last stage of the installation wizard.
>3. Open a new command prompt window from the start menu, so that changes to the PATH environment variable becomes effective. Install Jekyll and Bundler via: `gem install jekyll bundler`

注意：  
1. If you don’t know what version to install and you’re getting started with Ruby, we recommend you use Ruby+Devkit 2.6.X (x64) installer. 
2. 上述第二步执行前可更新源，路径：Ruby安装目录下的`msys64\etc\pacman.d`
>`Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/i686` 加入mirrorlist.mingw32首位  
`Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/mingw/x86_64` 加入mirrorlist.mingw64首位  
`Server = https://mirrors.tuna.tsinghua.edu.cn/msys2/msys/$arch` 加入mirrorlist.msys首位

## 本地运行
>1. start command prompt with ruby
>2. cd到博客本地路径下
>3. 执行`bundle install`安装依赖
>4. 编译：`jekyll b`
>5. 运行server：`jekyll s`
>6. 访问http://localhost:4000 （默认端口4000）






