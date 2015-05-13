# exception
unified exception framework（统一的异常框架）

各个业务系统的异常处理会存在一些问题：
1.异常定义不统一（几乎每个系统都定义了自己的业务异常类）；
2.异常处理混乱（有时候流程卡住，有时忽略了异常）;
3.记录异常日志格式混乱（组织架构调整，开始负责一个新系统时，可能会发现异常日志根本看不懂，甚至不知道到哪儿看异常日志）；
4.异常发生时，不能通知到相应责任人(异常经常被吞掉；往往用户提了技术支持后，我们才知道系统某个地方出了问题)；
等等……

所以我们做了一个异常模块，用来统一处理异常，总体的设计思路：
A).定义统一的异常，异常分级，定义异常errorCode,各个业务系统抛出统一定义的异常（这类似于的统一的接口返回格式）；
B).异常的处理统一放在异常模块，包括异常日志的记录，异常的通知等等；

目前为止，已经开发好的异常模块是以jar包的形式，嵌入到webapp中；考虑到将来异常模块的使用量和重要性，更好的一种设计可能是单独部署异常模块；

对于日常日志的记录，现在支持两种方式：
1.原来的记录logback日志文件的方式仍然保留，不过这个事情是由异常模块统一按照固定的格式来记录2
2.部分异常日志（按照异常等级划分）会持久化到mongodb里，关于使用mongo统一记录日志，这里说下选择mongo的原因：
  a)nosql,可以灵活的加字段；
  b)可以简单的通过加机器等方式扩展，不用担心存储空间问题；
  c)如果机器受限，可以固定mongo collection的大小，这样mongo会自动按照LRU算法复用之前的磁盘空间，无需手动清除日志。
  .….
  对于重要异常的通知，目前是支持发送rtx（企业用的一种聊天软件）,将来可能的方向有邮件、短信通知等；
