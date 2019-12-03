# 基于简单表格的问答系统
基于简单表格的问答系统

应用场景：
我们有一些简单表格数据：
人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

我想让机器能根据表格回答我们的问题： 张三兴趣是什么 ，查表得知看书。
在实际应用中，表格的行列字段可能变换，但我们希望程序能够自适应各种字段，支持百万以上条数据，都能读懂数据内容，解答关于各个表的自然语言问题。

这样只要更新表的数据，无需再编程。


安装方法：
文档和数据在dockerhub
安装docker后使用命令安装镜像：

docker pull zb1840/kbqa
需要在你的系统新建一个空的目录 /opt/user来放入日志和系统生成的各种临时文件。

mkdir /opt/user;

用命令启动docker里的服务服务：
docker run -it -v /opt/user:/opt/user -p 29999:29999 -p 3004:3004   -d zb1840/kbqa /bin/bash &

running......
三分钟后 ,服务加载数据启动完成，并自动生成自测日志  /opt/user/firstrun.log. 
然后你可以登陆系统自带的ssh服务管理虚拟环境中的服务，
ssh -p 29999  root@127.0.0.1
密码是六个空格，登陆。

现在可以开始使用服务，测试脚本/mnt/sdb/shell2/demo2.sh 

############################################################
硬件需求：8G内存 30G磁盘空间

使用方法：
cd /mnt/sdb/shell2/
cat 测试.txt 

人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

王五    厨师    30岁    男      电影

赵六    司机    30岁    女      画画


输入问题 ：李四的性别是什么

curl http://127.0.0.1:3004/api/xlsqa/test6/李四的性别是什么

输出：  李四 性别 女,

你可以更改 测试.txt 里的数据是csv格式，列之间用"\t" tab符号间隔。第一行是属性，第一列是实体，针对实体和属性问答。

默认支持相似语义句型的查询：
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的兴趣是什么
李四 兴趣 画画,
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的爱好是什么
李四 兴趣 画画


针对测试.txt这个csv文件，的问答对场景的学习的配置文件路径是/mnt/sdb/shell2/testqa22.txt
简单列出了 一个问答对  ：张三的个人爱好是什么 看书


运行demo2.pl后系统可以自动学习扩充 ，来会带 李四的个人爱好是什么
如果你加入一条“王五平时做什么 电影“到/mnt/sdb/shell2/testqa22.txt
运行demo2.pl.然后测试：

curl http://127.0.0.1:3004/api/xlsqa/test6/李四平时做什么
输出 画画


本项目中使用北大邹磊老师团队开发GSTORE开源数据库，。参考链接：
https://github.com/pkumod/gStore


张博资讯QQ：75926462，微信搜索：zb1840，电话13424269685

