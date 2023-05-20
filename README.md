---
ANTLR-自动词法语法分析器
---
Welcome to [YU.J.P's Blog](https://yjp520.github.io/)! 在这里你将探索到ANTLR自动词法语法分析器的相关知识。

### ANTLR简介

Antlr (Another Tool for Language Recognition) 是一款强大的语法分析器生成工具，可用于读取、处理、执行和翻译结构化的文本或者二进制文件。它被广泛应用于学术领域和工业生产实践，是众多语言、工具和框架的基石。我们可以使用Antlr来开发DSL（Domain Specific Language，领域特定语言），或者一些实用工具，比如配置文件读取器、遗留代码转换器和Json解析器等等。

### IDEA环境开发入门

- [CSDN参考博客链接 1](http://t.csdn.cn/QYoZf)
- [CSDN参考博客链接 2](http://t.csdn.cn/I7d8d)

**安装Antlr插件**

打开IDEA，在File—Settings—Plugins中，安装ANTLR v4 grammar plugin插件。

**第一个项目**

新建一个Maven项目，在pom文件中添加如下依赖，加在<\/project>之前。注意版本号要根据自己安装的antlr进行修改。

```xml
    <dependencies>
        <dependency>
            <groupId>org.antlr</groupId>
            <artifactId>antlr4-runtime</artifactId>
            <version>4.12.0</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.antlr</groupId>
                <artifactId>antlr4</artifactId>
                <version>4.12.0</version>
                <executions>
                    <execution>
                        <id>antlr</id>
                        <goals>
                            <goal>antlr4</goal>
                        </goals>
                        <phase>none</phase>
                    </execution>
                </executions>
                <configuration>
                    <outputDirectory>src/test/java</outputDirectory>
                    <listener>true</listener>
                    <treatWarningsAsErrors>true</treatWarningsAsErrors>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

新建一个.g4文件，用来编写要识别语言的词法规则和语法规则，比如这里我们的文件名为Hello.g4

```g4
grammar Hello;      // 定义一个名为Hello的语法，名字与文件名一致
r : 'Hello' ID      // 定义一个r规则，匹配一个关键字Hello和紧随其后的标识符ID
  | 'hello' ID;     // "|"是备选分支的分隔符
ID : [a-z]+;        // 定义ID标识符，由小写字符组成
WS : [ \t\r\n]+ -> skip;    // 忽略空格、Tab、换行以及\r （Windows）
```

然后我们就可以使用Antlr工具来将该语法文件转换成可以识别该语法文件所描述语言的程序。

不过在此之前，我们需要先配置下生成的程序输出路径，右键Hello.g4，找到 **Configure ANTLR**。

接着配置文件的输出路径和目录

| Term | Setting |
| -------- | ------------- | 
| output directory where all output is generated... | [输出文件路径hello] |
| package/namespace for the generated code... | [hello] |

接着我们就可以使用antlr工具来将语法文件转换成可以识别该语法文件所描述语言的程序了。

右键Hello.g4，点击 **Generate ANTLR Recognizer** ，接着可以看到目录下面生成了许多文件

其中HelloLexer是词法分析器，HelloParser是语法分析器，HelloVistor和HelloListener是两种遍历树的机制，分别是访问者模式和监听器模式。

最后，我们就可以来编写主程序来调用上述代码了。

```java
import hello.HelloLexer;
import hello.HelloParser;
import org.antlr.v4.runtime.ANTLRInputStream;
import org.antlr.v4.runtime.CommonTokenStream;
 
public class HelloMain {
    public static void run(String expr) throws Exception{
        // 对每一个输入的字符串，构造一个 ANTLRStringStream 流 in
        ANTLRInputStream input = new ANTLRInputStream(expr);
        // 用 in 构造词法分析器 lexer，词法分析的作用是将字符聚集成单词或者符号
        HelloLexer lexer = new HelloLexer(input);
        // 用词法分析器 lexer 构造一个记号流 tokens
        CommonTokenStream tokens = new CommonTokenStream(lexer);
        // 再使用 tokens 构造语法分析器 parser,至此已经完成词法分析和语法分析的准备工作
        HelloParser parser = new HelloParser(tokens);
        // 最终调用语法分析器的规则 r（这个是我们在Hello.g4里面定义的那个规则），完成对表达式的验证
        parser.r();
    }
 
    public static void main(String[] args) throws Exception{
        String[] testStr={
                "Hello world",
                "hello world",
                "hi world"
        };
        for(String s : testStr){
            System.out.println("Input: " + s);
            run(s);
        }
    }
}
```

最后运行结果如下：

    Input: Hello world
    Input: hello world
    Input: hi world
    line 1:0 mismatched input 'hi' expecting {'Hello', 'hello'}

    Process finished with exit code 0

除了以上方式，我们还可以使用文法可视化工具来测试语法规则

- 在Hello.g4中选择一个语法规则比如r，右键选择Test Rule r

- 在ANTLR Preview中输入要校验的文本，比如hello world

### 开发进阶

敬请期待！