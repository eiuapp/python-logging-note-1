https://www.cnblogs.com/zhbzz2007/p/5943685.html

[python logging模块](https://www.cnblogs.com/dahu-daqing/p/7040764.html)
======================================================================

参考:[老顽童log模块](http://www.cnblogs.com/zhbzz2007/p/5943685.html),讲的很细致,基本上拿到手就可以直接用了,很赞

1 logging模块简介
=============

logging模块是Python内置的标准模块，主要用于输出运行日志，可以设置输出日志的等级、日志保存路径、日志文件回滚等；相比print，具备如下优点：

1.  可以通过设置不同的日志等级，在release版本中只输出重要信息，而不必显示大量的调试信息；
2.  print将所有信息都输出到标准输出中，严重影响开发者从标准输出中查看其它数据；logging则可以由开发者决定将信息输出到什么地方，以及怎么输出；

2 logging模块使用
=============

2.1 基本使用
--------

配置logging基本的设置，然后在控制台输出日志，


运行时，控制台输出，

    2016-10-09 19:11:19,434 - __main__ - INFO - Start print log
    2016-10-09 19:11:19,434 - __main__ - WARNING - Something maybe fail.
    2016-10-09 19:11:19,434 - __main__ - INFO - Finish

logging中可以选择很多消息级别，如debug、info、warning、error以及critical。通过赋予logger或者handler不同的级别，开发者就可以只输出错误信息到特定的记录文件，或者在调试时只记录调试信息。

例如，我们将logger的级别改为DEBUG，再观察一下输出结果，

```
logging.basicConfig(level = logging.DEBUG,format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
```
控制台输出，可以发现，输出了debug的信息。

    2016-10-09 19:12:08,289 - __main__ - INFO - Start print log
    2016-10-09 19:12:08,289 - __main__ - DEBUG - Do something
    2016-10-09 19:12:08,289 - __main__ - WARNING - Something maybe fail.
    2016-10-09 19:12:08,289 - __main__ - INFO - Finish

logging.basicConfig函数各参数：

- level：设置日志级别，默认为logging.WARNNING；

- filename：指定日志文件名；

- filemode：和file函数意义相同，指定日志文件的打开模式，'w'或者'a'；

- datefmt：指定时间格式，同time.strftime()；

- stream：指定将日志的输出流，可以指定输出到sys.stderr，sys.stdout或者文件，默认输出到sys.stderr，当stream和filename同时指定时，stream被忽略；

- format：指定输出的格式和内容，format可以输出很多有用的信息，

        format下参数：作用
        
        %(levelno)s：打印日志级别的数值
        %(levelname)s：打印日志级别的名称
        %(pathname)s：打印当前执行程序的路径，其实就是sys.argv[0]
        %(filename)s：打印当前执行程序名
        %(funcName)s：打印日志的当前函数
        %(lineno)d：打印日志的当前行号
        %(asctime)s：打印日志的时间
        %(thread)d：打印线程ID
        %(threadName)s：打印线程名称
        %(process)d：打印进程ID
        %(message)s：打印日志信息


2.2 将日志写入到文件
------------

**2.2.1 将日志写入到文件**

设置logging，创建一个FileHandler，并对输出消息的格式进行设置，将其添加到logger，然后将日志写入到指定的文件中，


log.txt中日志数据为，

    2016-10-09 19:01:13,263 - __main__ - INFO - Start print log
    2016-10-09 19:01:13,263 - __main__ - WARNING - Something maybe fail.
    2016-10-09 19:01:13,263 - __main__ - INFO - Finish

**2.2.2 将日志同时输出到屏幕和日志文件**

logger中添加StreamHandler，可以将日志输出到屏幕上，


可以在log.txt文件和控制台中看到，

    2016-10-09 19:20:46,553 - __main__ - INFO - Start print log
    2016-10-09 19:20:46,553 - __main__ - WARNING - Something maybe fail.
    2016-10-09 19:20:46,553 - __main__ - INFO - Finish

可以发现，logging有一个日志处理的主对象，其他处理方式都是通过addHandler添加进去，logging中包含的handler主要有如下几种，

    handler名称：位置；作用
    
    StreamHandler：logging.StreamHandler；日志输出到流，可以是sys.stderr，sys.stdout或者文件
    FileHandler：logging.FileHandler；日志输出到文件
    BaseRotatingHandler：logging.handlers.BaseRotatingHandler；基本的日志回滚方式
    RotatingHandler：logging.handlers.RotatingHandler；日志回滚方式，支持日志文件最大数量和日志文件回滚
    TimeRotatingHandler：logging.handlers.TimeRotatingHandler；日志回滚方式，在一定时间区域内回滚日志文件
    SocketHandler：logging.handlers.SocketHandler；远程输出日志到TCP/IP sockets
    DatagramHandler：logging.handlers.DatagramHandler；远程输出日志到UDP sockets
    SMTPHandler：logging.handlers.SMTPHandler；远程输出日志到邮件地址
    SysLogHandler：logging.handlers.SysLogHandler；日志输出到syslog
    NTEventLogHandler：logging.handlers.NTEventLogHandler；远程输出日志到Windows NT/2000/XP的事件日志
    MemoryHandler：logging.handlers.MemoryHandler；日志输出到内存中的指定buffer
    HTTPHandler：logging.handlers.HTTPHandler；通过"GET"或者"POST"远程输出到HTTP服务器

**2.2.3 日志回滚**

使用RotatingFileHandler，可以实现日志回滚，



可以在工程目录中看到，备份的日志文件，

    2016/10/09  19:36               732 log.txt
    2016/10/09  19:36               967 log.txt.1
    2016/10/09  19:36               985 log.txt.2
    2016/10/09  19:36               976 log.txt.3

2.3 设置消息的等级
-----------

可以设置不同的日志等级，用于控制日志的输出，

    日志等级：使用范围
    
    FATAL：致命错误
    CRITICAL：特别糟糕的事情，如内存耗尽、磁盘空间为空，一般很少使用
    ERROR：发生错误时，如IO操作失败或者连接问题
    WARNING：发生很重要的事件，但是并不是错误时，如用户登录密码错误
    INFO：处理请求或者状态变化等日常事务
    DEBUG：调试过程中使用DEBUG等级，如算法中每个循环的中间状态

2.4 捕获traceback
---------------

Python中的traceback模块被用于跟踪异常返回信息，可以在logging中记录下traceback，

代码，



控制台和日志文件log.txt中输出，

    Start print log
    Something maybe fail.
    Faild to open sklearn.txt from logger.error
    Traceback (most recent call last):
      File "G:\zhb7627\Code\Eclipse WorkSpace\PythonTest\test.py", line 23, in <module>
        open("sklearn.txt","rb")
    IOError: [Errno 2] No such file or directory: 'sklearn.txt'
    Finish

也可以使用logger.exception(msg,\_args)，它等价于logger.error(msg,exc\_info = True,\_args)，

将

    logger.error("Faild to open sklearn.txt from logger.error",exc_info = True)

替换为，

    logger.exception("Failed to open sklearn.txt from logger.exception")

控制台和日志文件log.txt中输出，

    Start print log
    Something maybe fail.
    Failed to open sklearn.txt from logger.exception
    Traceback (most recent call last):
      File "G:\zhb7627\Code\Eclipse WorkSpace\PythonTest\test.py", line 23, in <module>
        open("sklearn.txt","rb")
    IOError: [Errno 2] No such file or directory: 'sklearn.txt'
    Finish

2.5 多模块使用logging
----------------

主模块mainModule.py，

子模块subModule.py，


执行之后，在控制和日志文件log.txt中输出，

    2016-10-09 20:25:42,276 - mainModule - INFO - creating an instance of subModule.subModuleClass
    2016-10-09 20:25:42,279 - mainModule.sub.module - INFO - creating an instance in SubModuleClass
    2016-10-09 20:25:42,279 - mainModule - INFO - calling subModule.subModuleClass.doSomething
    2016-10-09 20:25:42,279 - mainModule.sub.module - INFO - do something in SubModule
    2016-10-09 20:25:42,279 - mainModule.sub.module - INFO - finish something in SubModuleClass
    2016-10-09 20:25:42,279 - mainModule - INFO - done with  subModule.subModuleClass.doSomething
    2016-10-09 20:25:42,279 - mainModule - INFO - calling subModule.some_function
    2016-10-09 20:25:42,279 - mainModule.sub - INFO - call function some_function
    2016-10-09 20:25:42,279 - mainModule - INFO - done with subModule.some_function

首先在主模块定义了logger'mainModule'，并对它进行了配置，就可以在解释器进程里面的其他地方通过getLogger('mainModule')得到的对象都是一样的，不需要重新配置，可以直接使用。定义的该logger的子logger，都可以共享父logger的定义和配置，所谓的父子logger是通过命名来识别，任意以'mainModule'开头的logger都是它的子logger，例如'mainModule.sub'。

实际开发一个application，首先可以通过logging配置文件编写好这个application所对应的配置，可以生成一个根logger，如'PythonAPP'，然后在主函数中通过fileConfig加载logging配置，接着在application的其他地方、不同的模块中，可以使用根logger的子logger，如'PythonAPP.Core'，'PythonAPP.Web'来进行log，而不需要反复的定义和配置各个模块的logger。

3 通过JSON或者YAML文件配置logging模块
===========================

尽管可以在Python代码中配置logging，但是这样并不够灵活，最好的方法是使用一个配置文件来配置。在Python 2.7及以后的版本中，可以从字典中加载logging配置，也就意味着可以通过JSON或者YAML文件加载日志的配置。

3.1 通过JSON文件配置
--------------

JSON配置文件，

    {
        "version":1,
        "disable_existing_loggers":false,
        "formatters":{
            "simple":{
                "format":"%(asctime)s - %(name)s - %(levelname)s - %(message)s"
            }
        },
        "handlers":{
            "console":{
                "class":"logging.StreamHandler",
                "level":"DEBUG",
                "formatter":"simple",
                "stream":"ext://sys.stdout"
            },
            "info_file_handler":{
                "class":"logging.handlers.RotatingFileHandler",
                "level":"INFO",
                "formatter":"simple",
                "filename":"info.log",
                "maxBytes":"10485760",
                "backupCount":20,
                "encoding":"utf8"
            },
            "error_file_handler":{
                "class":"logging.handlers.RotatingFileHandler",
                "level":"ERROR",
                "formatter":"simple",
                "filename":"errors.log",
                "maxBytes":10485760,
                "backupCount":20,
                "encoding":"utf8"
            }
        },
        "loggers":{
            "my_module":{
                "level":"ERROR",
                "handlers":["info_file_handler"],
                "propagate":"no"
            }
        },
        "root":{
            "level":"INFO",
            "handlers":["console","info_file_handler","error_file_handler"]
        }
    }

通过JSON加载配置文件，然后通过logging.dictConfig配置logging，



3.2 通过YAML文件配置
--------------

通过YAML文件进行配置，比JSON看起来更加简介明了，


通过YAML加载配置文件，然后通过logging.dictConfig配置logging，



4 Reference
===========

[每个 Python 程序员都要知道的日志实践](http://dataunion.org/19906.html)

[Python标准模块logging](http://blog.csdn.net/fxjtoday/article/details/6307285)

[python 的日志logging模块学习](http://www.cnblogs.com/dkblog/archive/2011/08/26/2155018.html)
