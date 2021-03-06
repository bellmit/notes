## 参考博文
[深入浅出java常量池](https://www.cnblogs.com/syp172654682/p/8082625.html)
[深入了解Java字符串常量池](https://zhuanlan.zhihu.com/p/52710835)
[Java中几种常量池的区分](http://tangxman.github.io/2015/07/27/the-difference-of-java-string-pool/)⚠️
[请别再拿“String s = new String("xyz");创建了多少个String实例”来面试了吧](https://www.iteye.com/blog/rednaxelafx-774673)⚠️
[深入解析String#intern](https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html)⚠️
[彻底弄懂字符串常量池等相关问题](https://www.cnblogs.com/gxyandwmm/p/9495923.html)⚠️
[Java String 综述(上篇)](https://blog.csdn.net/justloveyou_/article/details/52556427)⚠️
[Java String 综述(下篇)](https://blog.csdn.net/justloveyou_/article/details/60983034)⚠️


[TOC]


# String的相等判断
```java
   public static void stringEquals() {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = "Hel" + "lo";
        String s4 = "Hel" + new String("lo");
        String s5 = new String("Hello");
        String s6 = s5.intern();
        String s7 = "H";
        String s8 = "ello";
        String s9 = s7 + s8;

        //由”“定义的字面量字符串在编译期间就会被放入字符串常量池中，s1和s2指向同一个地址
        System.out.println(s1 == s2);  // true
        //使用“”字面量字符串拼接的字符串在编译期间会被常量折叠成拼接后的字符串并且加入字符串常量池中
        System.out.println(s1 == s3);  // true
        //new String("lo")只有在运行期才会确定，所以在s4在运行期才可以确定。
        System.out.println(s1 == s4);  // false
        //虽然s7、s8两个是字面量，但是对于s9来说是用变量拼接的，只能等到运行期确定，在堆中创建s7和s8拼接后的对象
        System.out.println(s1 == s9);  // false
        System.out.println(s4 == s5);  // false
        /*intern会判断字符串常量池中是否有hello这个字符串，
        如果有则直接返回该字符串在常量池中的地址，否则将该字符串放入常量池中再返回地址。
        s1中的hello和s5的堆对象指向同一个字符串常量池中的hello地址，
        所以s6指向的就直接是常量池中的hello，和s1相同
        */
        System.out.println(s1 == s6);  // true
    }
```
```java
String a="a";
String b="b";
String c="c";
String s=a+b+c;
//上述代码其实会被执行成以下。在编译期间生成StringBuilder对象进行append操作放入class文件中
StringBuilder builder=new StringBuilder();
builder.append(a);
builder.append(b);
builder.append(c);
String s=builder.toString();

String s="a"+"b"+c;
//上述代码其实会被执行成以下
String s=new StringBuilder().append("ab").append(c).toString();

String s=a+"b"+"c";
//上述代码其实会被执行成以下
String s=new StringBuilder().append(a).append("b").toString("c");
```

对于以下代码，final常量在编译期间就会被放进常量池中，所以在编译期间能确定A和B，那么s = A + B相当于s="ab"+"cd"
```java
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B

public static void main(String[] args) {
     String s = A + B;  // 将两个常量用+连接对s进行初始化 
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
//s等于t，它们是同一个对象
```