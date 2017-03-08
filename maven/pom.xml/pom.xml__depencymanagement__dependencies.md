#dependencies与dependencyManagement的区别

>在上一个项目中遇到一些jar包冲突的问题，之后还有很多人分不清楚dependencies与dependencyManagement的区别，本篇文章将这些区别总结下来。

###1、DepencyManagement应用场景
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们的项目模块很多的时候，我们使用Maven管理项目非常方便，帮助我们管理构建、文档、报告、依赖、scms、发布、分发的方法。可以方便的编译代码、进行依赖管理、管理二进制库等等。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;于我们的模块很多，所以我们又抽象了一层，抽出一个itoo-base-parent来管理子项目的公共的依赖。为了项目的正确运行，必须让所有的子项目使用依赖项的统一版本，必须确保应用的各个项目的依赖项和版本一致，才能保证测试的和发布的是相同的结果。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在我们项目顶层的POM文件中，我们会看到dependencyManagement元素。通过它元素来管理jar包的版本，让子项目中引用一个依赖而不用显示的列出版本号。Maven会沿着父子层次向上走，直到找到一个拥有dependencyManagement元素的项目，然后它就会使用在这个dependencyManagement元素中指定的版本号。

来看看我们项目中的应用：
<div align=center><img width="150" height="150" src="http://img.blog.csdn.net/20150721204949922?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"/></div>

####Itoo-base-parent(pom.xml)
```
<dependencyManagement>  
        <dependencies>  
            <dependency>  
                <groupId>org.eclipse.persistence</groupId>  
                <artifactId>org.eclipse.persistence.jpa</artifactId>  
                <version>${org.eclipse.persistence.jpa.version}</version>  
                <scope>provided</scope>  
            </dependency>  
              
            <dependency>  
                <groupId>javax</groupId>  
                <artifactId>javaee-api</artifactId>  
                <version>${javaee-api.version}</version>  
            </dependency>  
        </dependencies>  
    </dependencyManagement>  
```


####Itoo-base(pom.xml)
```
<!--继承父类-->  
<project>
    <parent>  
        <artifactId>itoo-base-parent</artifactId>  
        <groupId>com.tgb</groupId>  
  
        <version>0.0.1-SNAPSHOT</version>  
        <relativePath>../itoo-base-parent/pom.xml</relativePath>  
    </parent>  
        <modelVersion>4.0.0</modelVersion>  
        <artifactId>itoo-base</artifactId>  
        <packaging>ejb</packaging>  
          
    <!--依赖关系-->  
    <dependencies>  
        <dependency>  
            <groupId>javax</groupId>  
            <artifactId>javaee-api</artifactId>  
        </dependency>  
          
        <dependency>  
            <groupId>com.fasterxml.jackson.core</groupId>  
            <artifactId>jackson-annotations</artifactId>  
        </dependency>  
          
        <dependency>  
            <groupId>org.eclipse.persistence</groupId>  
            <artifactId>org.eclipse.persistence.jpa</artifactId>  
            <scope>provided</scope>  
        </dependency>  
    </dependencies>  
</project>  
```

>这样做的好处：统一管理项目的版本号，确保应用的各个项目的依赖和版本一致，才能保证测试的和发布的是相同的成果，因此，在顶层pom中定义共同的依赖关系。同时可以避免在每个使用的子项目中都声明一个版本号，这样想升级或者切换到另一个版本时，只需要在父类容器里更新，不需要任何一个子项目的修改；如果某个子项目需要另外一个版本号时，只需要在dependencies中声明一个版本号即可。子类就会使用子类声明的版本号，不继承于父类版本号。


###2、Dependencies
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;相对于dependencyManagement，所有生命在dependencies里的依赖都会自动引入，并默认被所有的子项目继承。



































