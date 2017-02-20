#正确处理下载文件时HTTP头的编码问题（Content-Disposition）

>###应用场景
最近在做一个基于RPC提供对外接口的项目。其中一个接口比较特殊提供的是二进制流，此接口不能用RPC协议，只能提供HTTP接口（即一个普通的下载接口）。



``` java
一开始，我的代码是这样的，文件后缀是pdf：
     response.setHeader("Content-Disposition", "attachment;filename=\"" + URLEncoder.encode(fileName +".pdf", "UTF-8"));
     response.setHeader("Content-Type", "application/pdf");
     response.setHeader("Content-Length", pdfOutputStream.size()+"");
```

>上面的头部配置出现的问题是，文件在fixfox下载时文件名是未进行decode的一堆乱七八糟的文字。


###经网上查证,正确的配置应该是这样的：

```
   Content-Disposition: attachment;
                     filename="$encoded_fname";
                     filename*=utf-8''$encoded_fname
```

###原理：

>2010年 RFC 5987 发布，正式规定了 HTTP Header 中多语言编码的处理方式采用 parameter*=charset'lang'value 的格式，其中：

* charset 和 lang 不区分大小写。
* lang 是用来标注字段的语言，以供读屏软件朗诵或根据语言特性进行特殊渲染，可以留空。
* value 根据 RFC 3986 Section 2.1 使用百分号编码，并且规定浏览器至少应该支持 ASCII 和 UTF-8 。
* 当 parameter 和 parameter* 同时出现在 HTTP 头中时，浏览器应当使用后者。

>其好处是保持了向前兼容性：一来 HTTP 头仍然是 ASCII-only ，二来不支持该标准的旧版浏览器会按照当年 RFC 2616 的规定，把 parameter* 整体当作一个 field name ，从而当作一个未知的字段来忽略。随后，2011年 RFC 6266 发布，正式将 Content-Disposition 纳入 HTTP 标准，并再次强调了 RFC 5987 中多语言编码的方法，还给出了一个范例用于解决向后兼容的问题：


```
Content-Disposition: attachment;
                     filename="EURO rates";
                     filename*=utf-8''%e2%82%ac%20rates
                     
```

这个例子里，filename 的值是一个同义英语词组——这样符合 RFC 2616 ，普通的字段不应当被编码；至于使用 UTF-8 只是因为它是标准中强制要求必须支持的。然而，如果我们再仔细想想——目前市场上常见的旧版本浏览器多为 IE 。如此一来，我们可以适当变通一下，将 filename 字段也直接使用百分号编码后的字符串：
```
Content-Disposition: attachment;
                     filename="%e2%82%ac%20rates.txt";
                     filename*=utf-8''%e2%82%ac%20rates.txt
                     
```

#总结
```
对于较新的 Firefox 、 Chrome 、 Opera 、 Safari 等浏览器，都支持并会使用新标准规定的 filename* ，即使它们不会自动解码 filename 也无所谓了；而对于旧版本的IE浏览器，它们无法识别 filename* ，会将其自动忽略并使用旧的 filename（唯一的小瑕疵是必须要有一个英文后缀名）。这样一来就完美解决了多浏览器的多语言兼容问题，既不需要 UA 判断，也较为符合标准。

```











