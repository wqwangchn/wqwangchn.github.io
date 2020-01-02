####gollum安装

gollum是一个文档管理服务,依赖与Ruby，一般mac上会自带有Ruby

```
[sudo] gem install gollum
```

但是通过gem安装gollum的时候报错了,类似于下面这个错误（我当时的错误没有截图），<font color='red'>一般来说如果安装在源码.h文件报错一般会是开发包的问题。</font>这里其实就是少了`ruby-devel`包,所以接下来要安装ruby-devel包

```shell
ERROR:  Error installing bluecloth:
ERROR: Failed to build gem native extension.
 
/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby
extconf.rb
mkmf.rb can't find header files for ruby at
/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/ruby.h

```

在linux上一般通过yum去管理包，ruby-devel 在百度上查到很多可以根据rpm去安装的，但是在mac上只有brew ，并且brew下还没有ruby-devel这个包文件，所以想到 rvm是ruby环境管理器，所以就试着用下面的命令安装了ruby-devel这个ruby开发包

```
rvm install ruby-devel
```

然后

```
[sudo] gem install gollum
```

<font color='red'>成功</font>

默认gollum启动的接口为4567

http://127.0.0.1:4567 就可以新建一个wiki页面进行编辑了

