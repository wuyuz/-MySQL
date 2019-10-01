### Mysql之疑难杂病

1、莫名其妙的链接不上mysql，并报错1067

- 需要知道的是mysql，是可以不用安装就可以使用的，也就是使用解压的下载文件，配置一个系统路径，就可以使用了，但是难免会出现问题，其中在windows中，有时会由于系统的更新，使mysql无法使用。

  ![1557903687052](C:\Users\RootUser\AppData\Roaming\Typora\typora-user-images\1557903687052.png)

  以上报错，试过很多方法任然无法解决，最后通过将my.ini重命名为my-default.ini，就启动成功了

2、Every derived table must have its own alias，在mysql查询时出现这个错误，需要对表起别名。