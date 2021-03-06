## DOM解析
优点:  
1. XML树在内存中完整存储,因此可以直接修改其数据和结构.  
2. 可以通过该解析器随时访问XML树中的任何一个节点.  
3. DOM解析器的API在使用上也相对比较简单.  
缺点:  
如果XML文档体积比较大时,将文档读入内存是非常消耗系统资源的.  
使用场景:  
DOM 是用与平台和语言无关的方式表示 XML 文档的官方 W3C 标准.DOM 是以层次结构组织的节点的集合.这个层次结构允许开发人员在树中寻找特定信息.分析该结构通常需要加载整个文档和构造层次结构,然后才能进行任何工作.DOM是基于对象层次结构的.

## SAX解析
优点:  
SAX对内存的要求比较低,因为它让开发人员自己来决定所要处理的标签.特别是当开发人员只需要处理文档中所包含的部分数据时,SAX 这种扩展能力得到了更好的体现.  
缺点:  
用SAX方式进行XML解析时,需要顺序执行,所以很难访问到同一文档中的不同数据.此外,在基于该方式的解析编码过程也相对复杂.  
使用场景:  
对于含有数据量十分巨大,而又不用对文档的所有数据进行遍历或者分析的时候,使用该方法十分有效.该方法不用将整个文档读入内存,而只需读取到程序所需的文档标签处即可.  

## Xmlpull解析
android SDK提供了xmlpull api,xmlpull和sax类似,是基于流（stream）操作文件,然后根据节点事件回调开发者编写的处理程序.因为是基于流的处理,因此xmlpull和sax都比较节约内存资源,不会象dom那样要把所有节点以对橡树的形式展现在内存中.xmlpull比sax更简明,而且不需要扫描完整个流.
