[【狂神说Java】ElasticSearch7.6.x最新完整](https://www.bilibili.com/video/BV17a4y1x7zq?from=search&seid=15394082642132477196)

![image-20200929232952934](C:\Users\pc-hone\images\image-20200929232952934.png)

8.springboot集成 从原理

9.爬虫

10.实践   模拟全文检索

kibana

![image-20200930000342241](C:\Users\pc-hone\images\image-20200930000342241.png)

![image-20200930000356335](C:\Users\pc-hone\images\image-20200930000356335.png)

![image-20200930000803535](C:\Users\pc-hone\images\image-20200930000803535.png)

![image-20200930000925821](C:\Users\pc-hone\images\image-20200930000925821.png)

![image-20200930001224293](C:\Users\pc-hone\images\image-20200930001224293.png)

![image-20200930001234182](C:\Users\pc-hone\images\image-20200930001234182.png)

![image-20200930001246236](C:\Users\pc-hone\images\image-20200930001246236.png)

![image-20200930001305786](C:\Users\pc-hone\images\image-20200930001305786.png)

![image-20200930001325157](C:\Users\pc-hone\images\image-20200930001325157.png)

![image-20200930104953519](C:\Users\pc-hone\images\image-20200930104953519.png)



> 跨域

```
http.cors.enabled: true
http.cors.allow-origin: "*"
```

![image-20200930115245959](C:\Users\pc-hone\images\image-20200930115245959.png)



索引当作数据库     文档表中的数据    类型逐渐弃用

![image-20200930121901624](C:\Users\pc-hone\images\image-20200930121901624.png)

![image-20200930122123974](C:\Users\pc-hone\images\image-20200930122123974.png)

ELK 拆箱即用

![image-20200930141939688](C:\Users\pc-hone\images\image-20200930141939688.png)

![image-20200930142212261](C:\Users\pc-hone\images\image-20200930142212261.png)

文档：就是一条记录   索引 就是数据库



![=](C:\Users\pc-hone\images\image-20200930145057826.png)

**IK分词器**

![image-20200930145932846](C:\Users\pc-hone\images\image-20200930145932846.png)

```
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "中国共产党"
}

GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "中国共产党"
}
```

![image-20200930151914318](C:\Users\pc-hone\images\image-20200930151914318.png)

​	

自己需要的词需要自己加到分词器字典中

![image-20200930152618616](C:\Users\pc-hone\images\image-20200930152618616.png)

![image-20200930155233700](C:\Users\pc-hone\images\image-20200930155233700.png)

![image-20200930160001046](C:\Users\pc-hone\images\image-20200930160001046.png)

![image-20200930160247834](C:\Users\pc-hone\images\image-20200930160247834.png)

![image-20200930160326585](C:\Users\pc-hone\images\image-20200930160326585.png)

字段会设置默认字段类型

![image-20200930160756831](C:\Users\pc-hone\images\image-20200930160756831.png)

![image-20200930161201003](C:\Users\pc-hone\images\image-20200930161201003.png)

![image-20200930161215912](C:\Users\pc-hone\images\image-20200930161215912.png)

**复杂查询**

![image-20200930182214467](C:\Users\pc-hone\images\image-20200930182214467.png)

![image-20200930182425881](C:\Users\pc-hone\images\image-20200930182425881.png)

![image-20200930183115905](C:\Users\pc-hone\images\image-20200930183115905.png)

![image-20200930183218073](C:\Users\pc-hone\images\image-20200930183218073.png)

**布尔值查询**   must(and)

![image-20200930183926983](C:\Users\pc-hone\images\image-20200930183926983.png)

**shoud or**

![image-20200930184655392](C:\Users\pc-hone\images\image-20200930184655392.png)

![image-20200930184745371](C:\Users\pc-hone\images\image-20200930184745371.png)

**过滤器**

![image-20200930184906869](C:\Users\pc-hone\images\image-20200930184906869.png)

- gt 大于
- gte  大于等于
- lt 小于
- lte 小于等于

**匹配多个条件**

![image-20200930185217061](C:\Users\pc-hone\images\image-20200930185217061.png)

term 直接查询精确    match 会使用分词器（先分析再进行查询）

**两个类型  text   keyword不会被解析**

![image-20200930190619070](C:\Users\pc-hone\images\image-20200930190619070.png)

![image-20200930191210295](C:\Users\pc-hone\images\image-20200930191210295.png)

**高亮查询**

![image-20200930191353690](C:\Users\pc-hone\images\image-20200930191353690.png)

![image-20200930191503109](C:\Users\pc-hone\images\image-20200930191503109.png)

![image-20200930191600785](C:\Users\pc-hone\images\image-20200930191600785.png)

**继承SpringBoot** 

官方文档

版本问题  autoconfig     properies



![image-20201003153737418](C:\Users\pc-hone\images\image-20201003153737418.png)

![image-20201003154048922](C:\Users\pc-hone\images\image-20201003154048922.png)

![image-20201003162758898](C:\Users\pc-hone\images\image-20201003162758898.png)

![image-20201003163833458](C:\Users\pc-hone\images\image-20201003163833458.png)

![image-20201003170411545](C:\Users\pc-hone\images\image-20201003170411545.png)

![image-20201003173644669](C:\Users\pc-hone\images\image-20201003173644669.png)



![image-20201003183429265](C:\Users\pc-hone\images\image-20201003183429265.png)