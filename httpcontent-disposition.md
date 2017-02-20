#正确处理下载文件时HTTP头的编码问题（Content-Disposition）

>###应用场景
最近在做一个基于RPC提供对外接口的项目。其中一个接口比较特殊提供的是二进制流，此接口不能用RPC协议，只能提供HTTP接口（即一个普通的下载接口）。



``` java
一开始，我的代码是：
     response.setHeader("Content-Disposition", "attachment;filename=\"" + URLEncoder.encode(fileName +".pdf", "UTF-8") + "\";filename*=utf-8\'\'" +URLEncoder.encode(fileName +".pdf", "UTF-8"));
     response.setHeader("Content-Type", "application/pdf");
     response.setHeader("Content-Length", pdfOutputStream.size()+"");
```


