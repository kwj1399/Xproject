title: Hexo-NexT-GitHub记录

date: 2016-11-06 12:39:16

tags: [hexo,]

categories: 技术

---
持续更新...  

# 参考  
配置过程并不复杂，主要参考两个文档：  
>1.[NexT](http://theme-next.iissnan.com/getting-started.html)  
>2.[Hexo](https://hexo.io/zh-cn/docs/)  

版本：  
```
C:\Users\kyson>hexo -v
hexo-cli: 1.0.2
os: Windows_NT 10.0.10586 win32 x64
http_parser: 2.7.0
node: 6.9.1
v8: 5.1.281.84
uv: 1.9.1
zlib: 1.2.8
ares: 1.10.1-DEV
icu: 57.1
modules: 48
openssl: 1.0.2j
```
# 踩坑  
安装hexo-deploy-git部署到github上的时候,出现错误。  
**官方_config.yml配置：**   
```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```
**修改后：**  
```
deploy:  
  type: git
  repository: <repository url>
  branch: [branch]
  message: [message]
```
# Github
- 一个GitHub账号对应一个用户或者一个组织，GitHub会给这个用户分配一个域名：username.github.io，当用户访问这个域名时，GitHub会去解析username用户下，username.github.io项目的master分支。
- GitHub还为每个项目提供了域名，例如，你有一个项目名为blog， GitHub为这个项目提供的域名为username.github.io/blog，当你访问这个域名时，GitHub会去解析username用户下，blog项目的gh-pages 分支。  

所以，要搭建自己的博客，你可以选择建立名为username.github.io的项目， 在master分支下存放网站源代码，也可以选择建立名为 blog 的项目，在 gh-pages分支下存放网站源代码。
# Makedown&Hexo
- markdown标题标准写法写法需要在"#"和后面字符之间加一个空格,否则Hexo无法解析。
- 代码块用三个反单引号*```*包裹，一个反单引号会导致代码挤在一行。