# 字符类型

在说明字符类型之前，我想先说明下字符编码。

####字符编码

计算机是只识别二进制的，所以和数字一样我们要想办法把字符也映射为一串01的记号，数字类型采用十进制与二进制的转换解决了这个问题。对于字符我们只需要规范一个二进制数和字符之间对应关系的表就可以了。

**ASCII**

我们用两个字节，即8位（相应可以表示256个符号）来表示一些我们常用的字符，这一套字符对照表就是ASCII码，但是ASCII码只使用了后7位（第八位置为0）规定了与常用的128个字符的对应关系，这128个字符是a-z、A-Z、0-9以及一些标点符号和控制符号。

ASCII码是针对英文字符进行的规范，但不同的国家使用的语言不同（很明显我们就不用英文字符），不同国家规定的对照表也就不同，用多种不同的对照表去解析一个字符就会出现不同的结果。正因为不同编码规定的同一个二进制数表示的字符不同，所以如果编码和解码的规则不一致，就会出现乱码问题。因此我们需要一个通用的编码、解码规范（即是一个能将世界上常用的字符和二进制数进行对应的对照表）。

**Unicode**

为了解决上述问题，提出了Unicode字符集，它规定了字符和二进制数的对应关系，但没有给出具体的存储方式。因为英文字符用一个字节就足以表示，但是汉语字符可能要需要2个字节才能表示。如果以两个字节为单位进行存储，英文字符又会浪费掉一部分存储空间。

基于上述问题，就出现了不同的存储方案。

**UTF-8**

UTF-8采用了一种变长的存储方式解决了上述问题。为了让计算机能区分变长字符存储所需要的字节数，UTF-8做出如下规定：

1. 对于单字节字符，字节第一位是0，后面七位为这个符号的Unicode码，因此英文字母的Unicode码实际上和ASCII码是一致的。
2. 对于需要N个字节来存储的符号，第一个字节的前N位置为1，第N+1位置为0；后面N-1个字节的前两位都是10。比如三个字节存储的字符对应为:`1110xxxx 10xxxxxx 10xxxxxx`

这里UTF-8只是Unicode编码的一种存储实现，相应的还有UTF-16和UTF-32。

**UCS-2**

早期在做字符集统一的时候存在两个团队，一个是Unicode团队，另个一就是UCS团队，UCS团队提出的UCS-2采用两个字节16位的方式来存储一个字符，UCS-2是向下兼容ASCII的（也就是说UCS-2对ASCII中规定的字符与二进制数的对应关系予以保留，并追加更多字符）。但随后Unicode推出了UCS-2的超集Unicode-16，因为不需要两套规范，所以UCS-2就被并入Unicode-16。

**UTF-16**

UTF-6规定用十六进制、两个字节来存储字符，其范围是`0x0000 - 0xFFFF`
但两字节只能存储2<sup>16</sup>即65535个字符，这是不够的，因此要存储更多字符就需要两个存储单元。但是这就又出现一个问题，怎么判断两个存储单元是表示两个字符还是表示一个字符。为了解决这个问题，UTF-6编码规范将`0xD800 - 0xDFFF`这段设置为空，不对应任何字符。然后又把这一段空出来的空间分成两段，
`0xD800-0xDBFF`和`0xDC00-0xDFFF`，对于超出`0xFFFF`的字符追加的那一个存储单元映射为这两段的数值，并放在前面。这样计算机检测到第一个存储单元的两个字节在`0xD800-0xDBFF`和`0xDC00-0xDFFF`范围时就知道需要和后一个存储单元配合来定位为一个字符了。正是因为这个策略导致单个存储单元的UTF-16编码所能表示的字符数量要少。

    目前Java、C#、Object-C、JavaScript这些语言内部编码都采用的UTF-16（严格的说JavaScript采用的是UCS-2编码）。

####字符长度

刚刚也说过有些特殊字符需要两个存储单元来保存，所以采用UTF-16编码的语言通过length函数来取这个字符的长度时，就会返回2。

```java
//Java 字符𥊍长度
System.out.println("𥊍".length());    //2
```
```javascript
//JavaScript 字符𥊍长度
console.log("𥊍".length);    //2
```
```ruby
#Ruby 字符𥊍长度
puts "𥊍".length    #1
```
注意Ruby、Go、Rust这些语言已经开始使用UTF-8作为内部编码了，所以Ruby对特殊字符取长度返回1。

####字符和字符串

在Java和C++中字符串并不是一个原始的数据类型，而是一个字符序列，是一个类，字符才是基本数据类型。但是JavaScript和Ruby中字符串是作为基本数据类型提供的，相应的并没有细化字符的概念，字符只是长度为1的字符串。
```java
//Java 字符和字符串

public static void main(String args[]){
  char normal = 'a';
  char unicode = '\u039A';
  String str = "string";
  char[] chars = {'s', 't', 'r', 'i', 'n', 'g'};
  String charstr = new String(chars);
  
  System.out.println(normal);    //a
  System.out.println(unicode);   //K
  System.out.println(str);       //string
  System.out.println(charstr);   //string
}
```
```ruby
char = 'a'
string = 'string'

puts char      #a
puts string    #string
```
注意虽然Java中String是一个类，但字符串拷贝的时候是值类型，而不是引用类型。
```java
//Java 字符串是值类型，拷贝修改不会影响源

public static void main(String args[]){      
  char[] chars = {'s', 't', 'r', 'i', 'n', 'g'};
  String charStr = new String(chars);
  String equalCharStr = charStr;

  charStr = "string charStr";
  equalCharStr = "string equalCharStr";

  System.out.println(charStr);          //string charStr
  System.out.println(equalCharStr);     //string equalCharStr

  String str = "string";
  String equalStr = str;

  str = "string str";
  equalStr = "string equalStr";

  System.out.println(str);             //string str
  System.out.println(equalStr);        //string equalStr
}
```
Ruby和JavaScript中字符串本身就作为原始类型定义，所以字符串拷贝自然也是值类型拷贝。
```ruby
#Ruby 字符串拷贝，也是值类型拷贝

str = "string"
equalStr = str

str = "string srt"
equalStr = "string equalStr"

puts str         #string str
puts equalStr    #string equalStr
```

####字符串修改及内存优化

这部分内容已经在2.1 基础类型与栈内存分配中有介绍，因为字符串是值类型，所以字符串频繁修改时会消耗大量内存，因此部分语言本身提供了类来优化内存的使用。

```java
//Java StringBuffer优化字符串内存占用

public static void main(String args[]){
  char[] chars = {'a', 'b', 'c'};
  String s = new String();
  StringBuffer sb = new StringBuffer();

  for(char c : chars){
    s+= c;
    sb.append(c);
  }
  System.out.println(s);     //abc
  System.out.println(sb);    //abc
}
```