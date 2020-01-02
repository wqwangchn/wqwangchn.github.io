1.sudo pip install  时， sudo: pip：找不到命令

```
解决方法：
1.修改文件 /etc/sudoers
   sudo vi /etc/sudoers
   修改  Defaults  env_reset  这一行内容 为   Defaults   !env_reset

2.修改文件 .bashrc
	sudo vi .bashrc
	在文件末尾添加  alias sudo='sudo env PATH=$PATH'
	
	source .bashrc
[附录sudo的作用的文章 https://www.ibm.com/developerworks/cn/aix/library/au-sudo/index.html]
```

2.centos7 使用yum进行install 出现 KeyboardInterrupt

错误信息 

File"/usr/lib/python2.7/site-packages/urlgrabber/grabber.py",line1517,in_do_perform 
raiseKeyboardInterrupt

```
解决方法：
1.打开文件 /usr/lib/python2.7/site-packages/urlgrabber/grabber.py 
2.在1510行 (通过::1510 可以定位到)找到代码  elif errcode in (42,55,56): 
3.修改上面代码为 elif errcode == 42:
4.保存退出即可
```



 