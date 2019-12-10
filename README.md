# 基于简单表格的问答系统
为什么要做这个工具？

如果你要做一套基于领域知识库的自然语言问答系统，可以把数据拆分成若干个excel表格，针对每个excel的自然语言查询，易于开发和升级维护。

应用场景：

你有一些简单表格数据：
----------------------------------
人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

王五    厨师    30岁    男      电影

赵六    司机    30岁    女      画画


------------------------------------

表格的第一列看作实体列表，第一行，看作属性列表，表中内容是值。

你想让机器能根据表格回答用户的问题： 张三兴趣是什么 ，查表得知--"看书"。

王五的性别是什么?

。。。。。。


在实际应用中，表格的行列字段可能变换，但我们希望程序能够自适应各种字段，支持百万以上条数据，读懂数据内容，解答关于各个表的自然语言问题。

有的问题是查询表中的值，有的问题是已知值查实体，或属性，或者值关联的实体的属性的另一个值......

这样任意update数据，字段，无需再编程。

把用户的自然语言问答需求，转化成对一个excel表格的自然语言查询，你的开发成本会降低很多。

如果你觉得系统支持的问题不够多，可以输入一条简单的问答示例，系统就能从你的例子里学习到你的回答逻辑和知识发现思路，完成触类旁通的字段问答。


------------------------------


<b>安装和启动服务方法：<b>

硬件需求：8G内存 30G磁盘空间
ubuntu linux 16.04+
docker版本Docker version 18.09.7，

在linux系统安装docker后使用命令安装镜像：

docker pull zb1840/kbqa:v1

在你的系统新建一个空的目录 /opt/user  来放入日志和系统生成的各种临时文件。

mkdir /opt/user

用命令启动docker里的服务服务：
docker run -it -v /opt/user:/opt/user -p 29999:29999 -p 3004:3004   -d zb1840/kbqa:v1 /bin/bash &

running......
三分钟后 ,服务加载数据启动完成，并自动生成自测日志  /opt/user/firstrun.log. 
然后你可以登陆系统自带的ssh服务管理虚拟环境中的服务，
ssh -p 29999  root@127.0.0.1
密码是六个空格，登陆。

现在可以开始使用服务，测试脚本/mnt/sdb/shell2/demo2.sh 


--------------------------------------------------------------

<b>使用方法：<b>

1.准备好数据文件，写入"测试.txt" .编码要求utf-8.
cd /mnt/sdb/shell2/

cat 测试.txt 

人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

王五    厨师    30岁    男      电影

赵六    司机    30岁    女      画画

2.运行./demo2.sh 进行数据处理。大约几秒钟后。

3.输入问题 ：李四的性别是什么

访问3004的端口服务：

运行命令：curl http://127.0.0.1:3004/api/xlsqa/test6/李四的性别是什么

输出：  李四 性别 女,


 curl http://127.0.0.1:3004/api/xlsqa/test6/谁的职业是老师
 
 输出：张三, 职业 老师
 


你可以更改 测试.txt 里的数据，csv格式，列之间用"\t" tab符号间隔。第一行是属性，第一列是实体，针对实体和属性问答。

默认支持相似语义句型的查询：
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的兴趣是什么
李四 兴趣 画画,
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的爱好是什么
李四 兴趣 画画

------------------------------

<b>更加智能的理解数据，从而回答更多问题？<b>
 
 你可以调教这个系统，使它学会你的问答解题模式。

基于问答例子的学习：
针对测试.txt这个csv文件，的问答对场景的学习的配置文件路径是/mnt/sdb/shell2/testqa22.txt
修改/mnt/sdb/shell2/testqa22.txt 后，运行./demo2.pl使之生效。
在testqa22.txt里输入一行：

张三的个人爱好是什么 看书

运行./demo2.pl
原本不能回答的问题“ 李四的个人爱好是什么” 现在也能回答了。

当你在/mnt/sdb/shell2/testqa22.txt 增加一条配置：

谁是司机  赵六

运行demo2.pl

输入问题：

”谁是老师“

系统就可以回答"张三"了



这个例子里，你在配置文件里用 谁是XX 答案是YY  交给了系统如何解决谁是XX这样的问题。

再如：

调教：Q:老师几岁  A:答案45岁

再问系统，Q:厨师几岁， A:30岁。


数值大小比较：

curl http://127.0.0.1:3004/api/xlsqa/test6/张三年龄大还是李四年龄大

输出：张三 .因为张三年龄  45岁,，李四 年龄  18岁,



其他更多复杂多跳问题正在开发测试中。


如果希望开启同义词泛化问题支持，需要在/opt/user/loaddic写入1 重启docker镜像服务即可。服务会载入向量词典帮助自动扩充多样化的问法。



---------------------------------------------------
<b>感谢<b>

本项目中使用北京大学邹磊老师团队开发GSTORE开源数据库。参考链接：
https://github.com/pkumod/gStore

语义模糊搜索插件（可选配置）使用NGT向量搜索引擎：
https://github.com/yahoojapan/NGT

本项目使用的相关软件归各方所有。
作者深圳狗尾草智能科技有限公司。张博QQ：75926462，微信搜索：zb1840，电话13424269685

----------------------------------------

版权声明

Note: This license has also been called the "Simplified BSD License" and the "FreeBSD License". See also the 3-clause BSD License.

Copyright 2019 Gowild

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
