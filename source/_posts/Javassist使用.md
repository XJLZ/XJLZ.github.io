---
title: Javassist
date: 2022-08-19 16:32:00
tags:
- Java
- 字节码
---

首先需要引入jar包：

```xml
<dependency>
  <groupId>org.javassist</groupId>
  <artifactId>javassist</artifactId>
  <version>3.27.0-GA</version>
</dependency>
```

有如下类对象：

```java
package com.example.demotools;
public class TestUtil {
    public TestUtil() {
    }

    public static String getName(String var0) {
        return "this is changed by javassis";
    }

    public static String getName2(String var0) {
        return "-23333-wwwww-222222222";
    }
}

```

修改代码

```
public static void main(String[] args) throws Exception {
        // 这一步是完整的jar包路径
        ClassPool.getDefault().insertClassPath("D:\\repository\\com\\example\\demotools\\0.0.1-SNAPSHOT\\demotools-0.0.1-SNAPSHOT2.jar");
        CtClass ctClass = ClassPool.getDefault().getCtClass("com.example.demotools.TestUtil");
        CtMethod getName = ctClass.getDeclaredMethod("getName");
        getName.setBody("{if(true){return \"this is 1\";}else{return \"this is not 1\";}}");
        getName.insertBefore("return $1;");
        CtMethod getName2 = ctClass.getDeclaredMethod("getName2");
        getName2.setBody("{String a;if(true){a=\"this is a FiledValue\";return a;}else{return $1;}}");
        // 这一步就是将破译完的代码放在桌面上
        ctClass.writeFile("D:\\tmp\\");

    }
```

修改后

```
package com.example.demotools;

public class TestUtil {
    public TestUtil() {
    }

    public static String getName(String var0) {
        return var0;
    }

    public static String getName2(String var0) {
        if (true) {
            String var1 = "this is a FiledValue";
            return var1;
        } else {
            return var0;
        }
    }
}
```

这一步之后，你会在D:\\tmp\发现一个com文件夹，没错，这个就是最重要的东西！！！
将你使用的jar包拷贝到桌面上解压，然后将刚才的文件复制到解压后的文件夹里面进行覆盖，然后将META-INF中 *.RSA 和 *.SF 文件删除后，重新打包。

```
jar cvfm xxxxx.jar META-INF/MANIFEST.MF ./
```

更多使用：https://cloud.tencent.com/developer/article/1815164