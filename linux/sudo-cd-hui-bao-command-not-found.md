>$cd 
~$cd /root
bash: cd: /root: 权限不够
~$sudo !!
sudo cd /root
sudo: cd: command not found
~$

也就是说,cd是shell内置的,不是普通的命令,所以不能通过sudo运行 
如果确实需要运行cd,可以先输入sudo -s,然后就可以运行cd了,不过发现变成root@hostname了,也就是说变成root登陆了.
引用一段比较好的英文解释
>cd is a shell built-in command. It cannot be run in a child process. The child process simply cannot change the working directory of its parent shell process.
Redirection also does not work with sudo for the same reason (redirection being a shell "thing")
sudo 'ls /root/restricted >/root/out.txt'
sudo: ls /root/restricted >/root/out.txt: command not found
 
 
注：sudo 是一种程序，用于提升用户的权限，在Linux中输入sodu就是调用这个程序提升权限，shell是一个命令解析器，sudo cd是错误的，因为cd是shell内置的，不是系统里面的，sudo可以运行系统带的命令，但无法用系统中一个软件中的命令。<br/>
cd是shell的内部命令。所谓shell是一个交互式的应用程序。shell执行外部命令的 时候，是通过fork/exec叉一个子进程，然后执行这个程序。sudo的意思是，以别人的权限叉起一个进程，并运行程序。而cd是内部命令，也就是说，是直接由shell运行的，不叉子进程。你在当前进程里当然不能提升进程的权限（其实也可以，不过得编程的时候写到代码里，然后再编译，而我们的 shell没有这个功能，否则岂不是太危险了？）