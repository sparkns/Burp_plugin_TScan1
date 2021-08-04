# Brup_plugin_TScan1
在一些攻防演练中，像Shiro、Fastjson等常见高危漏洞一直被高频利用。但在一些情况下，这些漏洞通过几轮的洗刷下来出现的频率会逐渐变少。在打点的时候，一些平时并不会去测试的漏洞可能就能在某次中进行利用，并且借助打下一个内网据点。而我本身在真实做法中并不会刻意的去找一些漏洞，如springboot 的一些rce漏洞。遇到的Springboot会比较多，而挨个测时间成本就高了。即便大型扫描器对于这些都能够实现，但个人并不喜欢上这些大型扫描器。 所以在此萌生了一个念头，编写burp的插件挂着后台进行被动扫描。模块化开发，添加多个漏洞进行被动扫描。写好以后后面则是赌运气(阳寿)的时候了。

[Brup插件开发手记](https://www.cnblogs.com/nice0e3/p/15085841.html)

### 插件实现功能

1. 扫描Springboot特征，扫描Stringboot漏洞接口
2. 扫描Amf漏洞接口
3. Cas扫描漏洞_AAAA特征
4. Jenkins通过漏洞版本探测漏洞

目前只提供漏洞探测，探测可能存在漏洞进行精准打击。实现逛街式挖洞。

### 效果演示图

![image](https://user-images.githubusercontent.com/42479546/128038703-289eb90d-b344-4c25-9114-de033c118c1f.png)

![image](https://user-images.githubusercontent.com/42479546/128038756-80ab06f6-17e4-4a2e-946f-b01b28d4dd1c.png)

### 模块添加与编写

编写一个类继承Vulns与实现Vuln接口，重写Is_Vuln方法实现逻辑。

例子，添加到漏洞列表中：

```java

Vuln_Data vuln_data = new Vuln_Data();
                vuln_data.setId(BurpExtender.Vuln_Data.size());
                vuln_data.setUrl(uri);
                vuln_data.setExtensionMethod(Vuln_Name);
                vuln_data.setStatusCode( map.get("code").toString());
                vuln_data.setRequestResponse(baseRequestResponse);
                vuln_data.setRequestMethod(BurpExtender.callbacks.getHelpers().analyzeRequest(baseRequestResponse).getMethod());
                vuln_data.setIssue("/messagebroker/amf 路径存在 !!!");
                vuln_data.setStartTime(StartDate);
                vuln_data.setEndTime(EndDate);

                BurpExtender.Vuln_Data.add(vuln_data);
                stdout.println("漏洞存在："+GetURL(baseRequestResponse)+Vnln_Path);
                BurpExtender.tags.fireTableDataChanged();
```

添加完成后在BurpExtender类中添加
```
vulns.add(new Amf_Vuln(stdout,callbacks));
```

即可实现添加插件漏洞模块，代码比较烂。可能会在后面开源

### 一些问题
部分漏洞依然存在一些已知误报问题，后续再进行改动。部分已经bug,下版本进行修复。
