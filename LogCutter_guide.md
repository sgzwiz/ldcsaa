# Log-Cutter #

![http://bcs.duapp.com/jessma/blog/201306/Project-Log-Cutter.png](http://bcs.duapp.com/jessma/blog/201306/Project-Log-Cutter.png)
  * 支持 Linux、Mac 和 Windows 等所有常见操作系统平台
  * 支持命令行交互式运行
  * 支持后台非交互式运行（Linux/MAC 下使用 daemon 进程实现，Windows 用系统 Service 实现）
  * 支持三种日志清理方式：删除日志文件、切割日志文件、归档日志文件
  * 支持对 GB18030、UTF-8、UTF-16LE、UTF-16BE 等常用日志文件类型进行切割
  * 高度可配置（程序执行周期、要删除的日志文件过期时间、要切割的日志文件阀值和保留大小等均可配置）


---


## 使用方法 ##

```
**************************************************************
**** LogCutter - JessMA Open Source, all rights reserved. ****
**************************************************************

一、环境要求
--------------------------------------------------
1) Java 版 本: JDK / JRE 1.6 以上
2) 依赖程序包: dom4j、log4j、ant、juniversalchardet
--------------------------------------------------

二、配置文件
--------------------------------------------------
1) 程序配置文件: conf/config.xml (默认)
	（示例参考：conf/config-template.xml）
2) 日志配置文件: conf/log4j2.xml (默认)
	（示例请参考：conf/log4j2.xml）
--------------------------------------------------

三、安装部署
（注 ：LogCutter 需要配置 ‘JAVA_HOME’ / ‘JRE_HOME’ 和 ‘CLASSPATH’ 系统环境变量）
--------------------------------------------------
1) 配置系统环境变量 ‘JAVA_HOME’（或 ‘JRE_HOME’） 和 ‘CLASSPATH’
2) 在 LogCutter配置文件（默认：conf/config.xml）中配置清理规则
3) 启动 LogCutter
--------------------------------------------------

四、启动方式
--------------------------------------------------
1) Windows
	A) 前台运行: > run.bat [ -f config-file ]
	
	B) 后台运行: > LogCutter.exe	{	
						-install-demand  (安装手动启动服务)
						-install-auto    (安装自动启动服务)
						-uninstall       (删除服务)
						-start           (启动服务)
						-stop            (停止服务)
						-status          (查看服务状态)
					    }

	*** 注 *** 
		@ LogCutter.exe 以 Windows 系统服务的方式运行，安装好后也可以通过 Windows 服务管理器进行管理
		@ LogCutter.exe 是 32 位程序，LogCutter_x64.exe 是 64 位程序，根据当前系统平台使用其中之一

	C) 单次运行: > run.bat -1 [ -f config-file ]

2) Linux / Unix
	A) 前台运行: $ run.sh [ -f config-file ]
	B) 后台运行: $ run.sh [ -f config-file ] -d
	C) 单次运行: $ run.sh -1 [ -f config-file ] [ -d ]

	*** 注 ***
	  @ 可以把 run.sh 启动命令加入 /etc/rc.d/rc.local 中，从而设置为开机时自动运行
	  @ 可以把 run.sh -1 放入 CronTab 中定期执行，并且不用常驻内存，如：
	    ## 30 2 * * 2,4,6 root /usr/local/LogCutter/bin/run.sh -1 > /dev/null
--------------------------------------------------
```


## 配置文件 ##

```
<?xml version="1.0" encoding="UTF-8"?>
<CONFIG xmlns="http://www.jessma.org"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.jessma.org http://www.jessma.org/schema/log-cutter-cfg-2.0.xsd">
    <global>
        <!-- 开始日志检查延时 (小时), 默认: 0 (立刻开始)
        
            1) 当指定为一个整数值时，执行器首次启动延时时间为该值设定的小时数

                （例如：12 小时后首次启动执行器）
                <start-check-delay>12</start-check-delay>
                
            2) 当指定为一个 ‘hh:mm’ 格式的值时，执行器首次启动时间为该时分值

                （例如：12 点 34 分首次启动执行器，如果当前时间超过 12 点 34 分则在明天的 12 点 34 分首次启动执行器）
                <start-check-delay>12:34</start-check-delay>
        -->
        <!-- 02 点 30 分首次启动执行器 -->
        <start-check-delay>02:30</start-check-delay>
        <!-- 日志检查间隔 (小时), 默认: 72 -->
        <check-interval></check-interval>
        <!-- Log4J 配置文件, 默认: ${CLASS_ROOT}/../conf/log4j.properties -->
        <log4j-config-file></log4j-config-file>
        <!-- 进程锁文件, 默认: ${CLASS_ROOT}/../${APP_NAME}.lock -->
        <lock-file></lock-file>
    </global>
    
    <!-- 要删除的日志文件列表（可配置多个，由 DelFileRunner 执行）
        1) DelFileRunner 会递归删除符合条件的文件夹及其中的所有文件
        2) 注意：在判断是否删除文件夹时，检测的是文件夹的最后修改时间，而不是其中文件的最后修改时间
        3) DelFileRunner 适用于清理 “定期产生新日志文件” 的应用程序日志
    -->
    <!-- delete-files.expire: 日志文件过期时间(天), 默认: 90 -->
    <delete-files expire="30">
        <!-- file.path: 文件所在目录, 必须填写, 不能包含通配符 -->
        <!-- file: 文件或文件夹名称, 必须填写, 可包含通配符 -->
        <file path="D:\LogCutter\logs">LogCutter.log*</file>
        <file path="D:\hMailServer\Logs">*.log</file>
        <file path="D:\Tomcat 6.0\logs">*.log</file>
    </delete-files>
    <delete-files expire="365">
        <file path="E:\backup">*</file>
    </delete-files>
    
    <!-- 要截断的日志文件列表（可配置多个，由 CutFileRunner 执行）
        1) CutFileRunner 只会扫描符合条件的文件，不会扫描文件夹
        2) CutFileRunner 会截断文件的前部内容，保留后部内容
        3) CutFileRunner 适用于清理 “日志文件不断追加增长” 的应用程序日志
    -->
    <!-- cut-files.threshold:    日志文件截断阀值(KB), 默认: 10240 -->
    <!-- cut-files.reserve:        日志文件保留内容(KB), 默认: 1024 -->
    <!--
         <!注!> 'cut-files.reserve' 是保留内容的近似值, 实际内容按行取整保留
            如下列日志文件内容:
                ...... ...... ......
                 line1: xxxxxxxxxxxxxxxxxxxxx
                 line2: xxxxxxxxxxPyyyyyyyyyy
                 line3: zzzzzzzzzzzzzzzzzzzzz
                 line4: zzzzzzzzzzzzzzzzzzzzz
                 ...... ...... ...... (EOF)
             'P'为定位得到的保留起点, 程序实际会在'P'点开始查找下一个换行符,
             从该换行符的后一个字符开始保留, 也就是从第三行开始保留到文件末尾
    -->
    <cut-files threshold="10240" reserve="512">
        <file path="D:\Apache-2.2\logs">*.log</file>
        <file path="D:\MySQL Server 5.1\data">*.err</file>
    </cut-files>
    
    <!-- 要归档的日志文件列表（可配置多个，由 ArcFileRunner 执行）
        1) ArcFileRunner 把符合条件的文件或文件夹压缩归档到指定目录，并删除原文件或文件夹
        2) 归档文件格式：{原文件/文件夹名称}_{系统时间}.zip
        3) 注意：在判断是否归档文件夹时，检测的是文件夹的最后修改时间，而不是其中文件的最后修改时间
        4) ArcFileRunner 适用于清理 “定期产生新日志文件或日志目录” 的应用程序日志
    -->
    <!-- archive-files.expire:            日志文件过期时间(天), 默认: 90 -->
    <!-- archive-files.archive-path:    日志文件归档目录 -->
    <archive-files expire="120" archive-path="E:\backup">
        <file path="D:\MySQL Server 5.1\data">mysql-bin.*</file>
    </archive-files>
</CONFIG>
```


---


## run.bat（Windows 平台） ##
```
@ECHO OFF

echo.

if "%JRE_HOME%" == "" goto :use_jdk
	set _JAVA_HOME="%JRE_HOME%"
	echo Using  JRE_HOME : %_JAVA_HOME%
	goto :set_classpath
	
:use_jdk
set _JAVA_HOME="%JAVA_HOME%"
echo Using JAVA_HOME : %_JAVA_HOME%

:set_classpath
set _CLASSPATH="%CLASSPATH%"
echo Using CLASSPATH : %_CLASSPATH%

set _JAVA=%_JAVA_HOME%\bin\java

set APP_PATH="%~dp0.."
set APP_CLASSPATH=%APP_PATH%\classes
set APP_LIBPATH=%APP_PATH%\lib
set APP_MAIN_CLASS=org.jessma.logcutter.main.LogCutter

echo.

@ECHO ON

%_JAVA% -Duser.dir=%APP_PATH% -Djava.ext.dirs=%APP_LIBPATH% -cp %APP_CLASSPATH%;%_CLASSPATH% %APP_MAIN_CLASS% %*
```

## run.sh（Unix/Linux 平台） ##
```
#!/bin/bash

echo

if [ "$JRE_HOME" != "" ]
then
    _JAVA_HOME="$JRE_HOME"
    echo Using  JRE_HOME : $_JAVA_HOME
else
    _JAVA_HOME="$JAVA_HOME"
    echo Using JAVA_HOME : $_JAVA_HOME
fi

_CLASSPATH="$CLASSPATH"
echo Using CLASSPATH : $_CLASSPATH

_JAVA=$_JAVA_HOME/bin/java

APP_PATH="$(cd "$(dirname "$0")" && pwd)/.."
APP_CLASSPATH=$APP_PATH/classes
APP_LIBPATH=$APP_PATH/lib
APP_MAIN_CLASS=org.jessma.logcutter.main.LogCutter

ARGS=
DAEMON='false'
CMD="$_JAVA -Duser.dir=$APP_PATH -Djava.ext.dirs=$APP_LIBPATH -cp $APP_CLASSPATH:$_CLASSPATH $APP_MAIN_CLASS"

echo

while (( $# > 0 ))
do
    if [ "$1" == '-d' ]
    then
        DAEMON='true'
    else
        ARGS="$ARGS $1"
    fi
    
    shift
done

if [ "$DAEMON" == 'false' ]
then
    $CMD $ARGS
else
    $CMD $ARGS &
fi
```


---


## 交互式操作演示（Windows 平台） ##

```
C:\>D:\Server\log-cutter\bin\run.bat

Using JAVA_HOME : "C:\Program Files\Java\JDK-1.7.0_21"
Using CLASSPATH : "C:\Program Files\Java\JDK-1.7.0_21\lib\dt.jar"

command line usage
------------------------------------------------------------
    HELP : Show help
    JOBS : Show jobs status
     CFG : Show configuration summary
     RUN : Schedule jobs manually
      !Q : Shutdown application
       ? : About me
------------------------------------------------------------
> help
command line usage
------------------------------------------------------------
    HELP : Show help
    JOBS : Show jobs status
     CFG : Show configuration summary
     RUN : Schedule jobs manually
      !Q : Shutdown application
       ? : About me
------------------------------------------------------------
> jobs
jobs summary (total: 3, active: 0)
------------------------------------------------------------
    1. DelFileRunner@16399041                     [  Idle  ]
    2. CutFileRunner@2583282                      [  Idle  ]
    3. ArcFileRunner@31342381                     [  Idle  ]
------------------------------------------------------------
> cfg
configuration summary
------------------------------------------------------------
[global]
    start-check-delay : 726   minutes
       check-interval : 4320  minutes
    log4j-config-file : /D:/Server/log-cutter/classes/../conf/log4j.properties
            lock-file : /D:/Server/log-cutter/classes/../LogCutter.lock
[delete-files] (expire: 30 days)
    1. D:\LogCutter\logs\LogCutter.log*
    2. D:\hMailServer\Logs\*.log
    3. D:\Tomcat 6.0\logs\*.log
[delete-files] (expire: 365 days)
    4. E:\backup\*
[cut-files] (threshold: 10240 KBs, reserve: 512 KBs)
    1. D:\Apache-2.2\logs\*.log
    2. D:\MySQL Server 5.1\data\*.err
[archive-files] (expire: 120 days, archive-path: 'E:\backup')
    1. D:\MySQL Server 5.1\data\mysql-bin.*
------------------------------------------------------------
> run
manual jobs are scheduled !
> ?
LogCutter 2.0.1 - JessMA Open Source, all rights reserved.
------------------------------------------------------------
    Description : schedule to DELETE, CUT and ARCHIVE text log files automatically or manually.
        Support : GB18030, UTF-8, UTF-16LE and UTF-16BE text file types.
          Usage : java org.jessma.logcutter.main.LogCutter [ -f <config-file> ]
                  (default config file is '/D:/Server/log-cutter/classes/../conf/config.xml')
------------------------------------------------------------
> !q
be about to shutdown, please wait ...
shutdown perfectly !

C:\>
```

## 后台运行演示（Mac OS X 平台） ##

```
[Kingfisher@Bruce-mbp ~] $ /opt/log-cutter/bin/run.sh -d

Using JAVA_HOME : /Library/Java/Home
Using CLASSPATH : /Library/Java/Home/lib/dt.jar

[Kingfisher@Bruce-mbp ~] $ 
LogCutter is running in background, use 'kill 4544' to stop me.

[Kingfisher@Bruce-mbp ~] $ kill 4544
[Kingfisher@Bruce-mbp ~] $ 
!! LogCutter received terminate signal !!
be about to shutdown, please wait ...
shutdown perfectly !

[Kingfisher@Bruce-mbp ~] $ 
```

## 单次运行演示（Mac OS X 平台） ##

```
[Kingfisher@Bruce-mbp ~] $ /opt/log-cutter/bin/run.sh -1

Using JAVA_HOME : /Library/Java/Home
Using CLASSPATH : /Library/Java/Home/lib/dt.jar

(running-only-once mode)
be about to shutdown, please wait ...
shutdown perfectly !
[Kingfisher@Bruce-mbp ~] $
```


---



## 运行日志 ##

```
14:24:55,687  INFO [main]: ->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->->
14:24:55,687  INFO [main]: starting: class org.jessma.logcutter.main.LogCutter ...
14:24:55,689  INFO [main]: configuration summary 
------------------------------------------------------------
[global]
    start-check-delay : 726   minutes
       check-interval : 4320  minutes
    log4j-config-file : /D:/Server/log-cutter/classes/../conf/log4j.properties
            lock-file : /D:/Server/log-cutter/classes/../LogCutter.lock
[delete-files] (expire: 30 days)
    1. D:\LogCutter\logs\LogCutter.log*
    2. D:\hMailServer\Logs\*.log
    3. D:\Tomcat 6.0\logs\*.log
[delete-files] (expire: 365 days)
    4. E:\backup\*
[cut-files] (threshold: 10240 KBs, reserve: 512 KBs)
    1. D:\Apache-2.2\logs\*.log
    2. D:\MySQL Server 5.1\data\*.err
[archive-files] (expire: 120 days, archive-path: 'E:\backup')
    1. D:\MySQL Server 5.1\data\mysql-bin.*
------------------------------------------------------------
14:26:22,867  INFO [pool-1-thread-1]: - - - - - - - -> start DelFileRunner <- - - - - - - -
14:26:22,867  INFO [pool-1-thread-2]: - - - - - - - -> start CutFileRunner <- - - - - - - -
14:26:22,867  INFO [pool-1-thread-2]: - - - - - - - ->   end CutFileRunner <- - - - - - - -
14:26:22,867  INFO [pool-1-thread-1]: - - - - - - - ->   end DelFileRunner <- - - - - - - -
14:26:22,868  INFO [pool-1-thread-2]: - - - - - - - -> start ArcFileRunner <- - - - - - - -
14:26:22,868  INFO [pool-1-thread-2]: - - - - - - - ->   end ArcFileRunner <- - - - - - - -
14:26:31,229  INFO [main]: stoping: class org.jessma.logcutter.main.LogCutter ...
14:26:31,229  INFO [main]: <-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-<-
```