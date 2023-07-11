# windows部署
## 1、下载
## 2、启动命令
`.\minio.exe server [存储位置] --address :[api端口号] --console-address :[后台端口号]
例：`.\minio.exe server C:\minio --address :9091 --console-address :9090`
## 3、系统常驻
1. 安装winsw
   https://github.com/winsw/winsw/releases
2. 修改WinSW的名字为minio-server.exe
3. 同目录下新建minio-server.xml文件
4. xml具体配置
``` xml
<service>
    <!-- 指定Windows内部用户标识服务的唯一id -->
    <id>minio-server</id>
    <!-- 指定Windows服务的简短唯一名称 -->
    <name>minio-server</name>
    <!-- 对服务的完整描述 -->
    <description>minio文件存储服务器</description>
    <!-- 可设置环境变量 -->
    <env name="MINIO_HOME" value="%BASE%"/>
    <!-- 指定要启动的可执行文件 -->
    <executable>%BASE%\minio.exe</executable>
    <!-- 指定传递参数给可执行文件,多个参数以空格间隔 -->
    <arguments>server "%BASE%\data"</arguments>
    <!-- 指定日志文件的目录,默认为配置文件所在的目录 -->
    <logpath>%BASE%\logs</logpath>
    <!-- 设置日志模式为按文件大小和时间滚动 -->
    <log mode="roll-by-size-time">
        <!-- 日志文件大小为10240kb生成新日志文件 -->
        <sizeThreshold>10240</sizeThreshold>
        <!-- 设置日志文件名按时间的格式 -->
        <pattern>yyyyMMdd</pattern>
        <!-- 设置每天按时间滚动日志的时间 -->
        <autoRollAtTime>00:00:00</autoRollAtTime>
        <!-- 文件保留天数(需要autoRollAtTime同时使用) -->
        <zipOlderThanNumDays>5</zipOlderThanNumDays>
        <!-- <zipDateFormat>yyyyMMdd</zipDateFormat> -->
    </log>
</service>
```
5. 执行`minio-server.exe install`安装服务
6. 安装完成后在任务管理器中找到名称为minio-server的服务右键-开始