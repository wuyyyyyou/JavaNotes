# 简介
* JSP标准标签库
* 作用:
  * 是JSTL实现JSP页面中的逻辑处理。如判断、循环等。

# 安装
* 官方下载地址:
  * `http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/`
* 解压后将`/lib/`文件夹下的两个jar包拷贝到`/WEB-INF/lib`下
* 将`/tld/`文件夹下的文件复制到`META-INF/tld`目录下
* 设置web.xml加入JSTL配置:
    ```xml
    <jsp-config>
        <taglib>  
            <taglib-uri>http://java.sun.com/jsp/jstl/core</taglib-uri> 
            <taglib-location>/META-INF/tld/c.tld</taglib-location>  
        </taglib>
        <taglib>  
            <taglib-uri>http://java.sun.com/jsp/jstl/functions</taglib-uri> 
            <taglib-location>/META-INF/tld/fn.tld</taglib-location>  
        </taglib> 
        <taglib>  
            <taglib-uri>http://java.sun.com/jsp/jstl/fmt</taglib-uri>  
            <taglib-location>/WEB-INF/fmt.tld</taglib-location>  
        </taglib>
    </jsp-config>
    ```
# IDEA安装方式
* 官网下载
* 两个jar包放到`/WEB-INF/lib`下
* 文件->项目结构中引入包

# JSTL核心标签
* 使用JSTL需要在头部加入以下代码:
  * `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

1. `<c:out>`
   * 用于在JSP中显示数据,就像`<%=...%>`
   * 属性:
     * value:显示的值(可以用EL表达式)
     * default:如果value为空或者空字符串时输出的默认值
     * escapeXml:过滤XML标签(删除字符串中的`<....>`内容)
       * 默认是true保留XML标签
       * 设置false会删除XML标签
2. `<c:set>`:保存数据
   * 属性:
     * var:保存的属性名称
     * value:保存的属性值
     * scope:保存的作用域
       * 填:page,request,session,application
       * 默认是page
3. `<c:remove>`:删除数据
   * 属性:
     * var:删除的数据名称
     * scope:删除的数据作用域
   * **注意:**set和remove执行后只会影响后面的属性值,前面的属性值还是依旧是之前赋的值。

* demo2.java

```java
req.setAttribute("name", "requestName");
req.getSession().setAttribute("name", "sessionName");
req.setAttribute("xml", "abc<abc>111");

req.getRequestDispatcher("/pages/day13/demo2.jsp").forward(req, resp);
```

* demo2.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<h1><c:out value="hello world!!!"></c:out></h1>
<h1><c:out value="${requestScope.name}"></c:out></h1>
<h1><c:out value="${sessionScope.name}"></c:out></h1>
<h1><c:out value="${requestScope.xml}"></c:out></h1>
<h1><c:out value="${requestScope.xml}" escapeXml="false"></c:out></h1>
<h1><c:out value="${requestScope.kongzhi}" default="默认"></c:out></h1>

<c:set var="name" value="test"></c:set>
<h1><c:out value="${pageScope.name}" default="page默认"></c:out></h1>
<c:set var="name" value="test" scope="request"></c:set>
<h1><c:out value="${requestScope.name}" default="request默认"></c:out></h1>

<c:remove var="name" scope="request"></c:remove>
<h1><c:out value="${requestScope.name}" default="request默认"></c:out></h1>
</body>
</html>
```

4. `<c:if>`:if判断
   * test属性:用于判断的式子，一般用EL表达式。
     * 数字判断:`<、>、==、>=、<=`，例子:`${a > 1}`
     * 字符串判断:`==`，例子:`${name == 'admin'}`
     * boolean值判断:`==`，例子:`${a == true}`
     * **注意:**如果比较表达式两边数据类型不一样会进行强制类型转换,如果无法强制转换那么将会报错。

* demo3.java

```java
req.setAttribute("flag", 1);

req.getRequestDispatcher("/pages/day13/demo3.jsp").forward(req, resp);
```

* demo3.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>
    <c:if test="${requestScope.flag == 0}">
        注册成功!
    </c:if>
    <c:if test="${requestScope.flag >=1}">
        注册失败!
    </c:if>
</h1>

</body>
</html>
```

5. `<c:choose>、<c:when>、<c:otherwise>`
   * 相当于if(){}else if(){}else{}
   * `<c:choose>`本身只能当作`<c:when>`和`<c:otherwise>`的父标签
   * `<c:when>`相当于if和else if
   * `<c:otherwise>`相当于else

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<c:choose>
  <c:when test="${flag == 0}">
    <h1>
      flag等于0
    </h1>
  </c:when>
  <c:when test="${flag == 1}">
    <h1>
      flag等于1
    </h1>
  </c:when>
  <c:otherwise>
    <h1>
      flag不等于0也不等于1
    </h1>
  </c:otherwise>
</c:choose>
</body>
</html>
```

6. `<c:forEach>`:基础迭代标签
   * 属性:
     * items:接收的集合
     * var:集合中的临时变量
     * start:开始元素下标
     * end:结束元素下标
     * step:步长,每隔step-1个元素取一个
     * varStatus:用于获得索引信息
       * vs.count:元素是迭代的第几次(从1开始计数)
       * vs.first:是否是迭代的首个元素
       * vs.last:是否是迭代的最后一个元素
       * vs.begin:其实索引,与begin属性值相同
       * vs.end
       * vs.step
       * vs.index:当前元素索引下标

* demo2.java

```java
List<String> strs = new ArrayList<>();
strs.add("s_0");
strs.add("s_1");
strs.add("s_2");
strs.add("s_3");
strs.add("s_4");
strs.add("s_5");

List<User> users = new ArrayList<>();

users.add(new User("张三", 12));
users.add(new User("李四", 19));
users.add(new User("王五", 16));
users.add(new User("Lucy", 13));

req.setAttribute("users", users);

req.setAttribute("strs", strs);
req.getRequestDispatcher("/pages/day14/demo2.jsp").forward(req, resp);
```

* demo2.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<c:forEach items="${requestScope.strs}" var="str" begin="0" end="5" step="2" varStatus="vs">
    <h5>
        当前元素是迭代的第:${vs.count}个元素,
        当前元素下标:${vs.index},
        是否是迭代的第一个元素:${vs.first},
        是否是迭代的最后一个元素:${vs.last},
        迭代起始位置:${vs.begin},
        迭代终止位置:${vs.end},
        迭代步长:${vs.step},
        元素:
        ${pageScope.str}
    </h5>
</c:forEach>

<table border="1">
    <tr>
        <td>行号</td>
        <td>姓名</td>
        <td>年龄</td>
    </tr>
    <c:forEach items="${requestScope.users}" var="user" varStatus="vs">
        <tr>
            <td>${vs.count}</td>
            <td>${user.name}</td>
            <td>${user.age}</td>
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

7. `<c:forTokens>`标签
   * 将字符串根据指定字符串分割成数组后循环输出
   * 属性:
     * items:要分割的字符串
     * delims:分隔符
     * var
     * begin
     * end
     * step
     * varStatus

* demo3.java

```java
String str = "Hello,,World,,!!!!";

req.setAttribute("str", str);
req.getRequestDispatcher("/pages/day14/demo3.jsp").forward(req, resp);
```

* demo3.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<c:forTokens items="${requestScope.str}" delims=",," var="s">
    <h1>${s}</h1>
</c:forTokens>
</body>
</html>
```

# JSTL格式化标签
* JSP页面头部加入以下代码:
* `<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>`

<br>

1. `<fmt:formatNumber>`:数字格式化标签
   * 属性:
   * var:变量名
   * value:数字值
   * scope:作用域
   * minIntegerDigits最小整数位数
   * maxIntegerDigits最大整数位数
   * minFractionDigits最小小数位数
   * maxFractionDigits最大小数位数
     * 最小位数不够补0
     * 最大位数超过则舍弃或四舍五入
   * groupingUsed:是否用`,`每隔三位分割数字
     * 默认为true,用`,`分割。
   * type属性:
     * NUMBER数字（默认保留三位小数）
       * 会自动省略数字最前方和小数最后方的0
     * PERCENT百分数（默认不保留小数）
     * CURRENCY货币，数字前加入货币符号（保留两位小数）
       * 默认使用当前浏览器的国家显示符号
       * 可以设置currencySymbo属性指定货币符号,例如`$`
   * pattern属性:自定义格式化
     * 0代表数字，例如`000.000`
       * 整数不够补0，超过依旧显示。
       * 小数不够补0，超过四舍五入。
       * 相当于设定了整数的最小位数和小数的最大和最小位数
     * #代表数字，例如`###.###`把补位0去掉，超过四舍五入
       * 和上面类似，只不过不够不会补0
       * 相当于设定了小数的最大位数
     * 可以用英文逗号来分割数字，例如`0000,00.000`表示每两位一分割
     * 最后加上%把当前数字乘上100%
       * 百分号后的小数位数是这个百分数的小数位数，不是原来的整数的小数位数，比如`1000.12%`小数位数是2不是4
     * 最前加上-展示负数
     * 最前加入货币符号展示货币

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<fmt:formatNumber var="num" value="0012345678.12745678" scope="page"
                  minIntegerDigits="10" maxFractionDigits="2"
                  groupingUsed="false">
</fmt:formatNumber>
<h1>${num}</h1>
<fmt:formatNumber var="num1" value="1234.120" type="NUMBER">
</fmt:formatNumber>
<h1>${num1}</h1>
<fmt:formatNumber var="num2" value="1234.1254" type="PERCENT">
</fmt:formatNumber>
<h1>${num2}</h1>
<fmt:formatNumber var="num3" value="1234.1254" type="CURRENCY"
                  currencySymbol="$">
</fmt:formatNumber>
<h1>${num3}</h1>
<fmt:formatNumber var="num4" value="0000001234.1254" pattern="00000.00000">
</fmt:formatNumber>
<h1>${num4}</h1>
<fmt:formatNumber var="num5" value="001234.1254" pattern="#######.#####">
</fmt:formatNumber>
<h1>${num5}</h1>
<fmt:formatNumber var="num6" value="001234.1254" pattern="0000,00.#####">
</fmt:formatNumber>
<h1>${num6}</h1>
<fmt:formatNumber var="num7" value="001234.1254" pattern="-$0000,00.#%">
</fmt:formatNumber>
<h1>${num7}</h1>
</body>
</html>
```

2. `<fmt:parseNumber>`:解析数字字符串
   * 属性:
     * var:变量名称
     * value:变量值
     * scope:作用域
     * integerOnly:是否解析小数
       * 默认位true
       * 设定为false只会取整数部分
     * type：
       * NUMBER：默认值，按照整数解析
         * 这里解析包括全部位数的小数
       * PERCENT:按照小数解析
       * CURRENCY:按照货币解析，默认浏览器所属区域。
         * 通过parseLocale属性设置国家
           * 美国是en_US
           * 其它区域去查询区域码即可
     * pattern：自定义解析
       * 在自定义开头或者结尾加入不同的头部或者尾部
       * 数字部分用一个#表示即可（默认integerOnly是true所以这里一个#也包含了小数，与上面的formatNumber不同）

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<fmt:parseNumber var="num" value="009876.987600" >
</fmt:parseNumber>
<h1>${num}</h1>
<fmt:parseNumber var="num1" value="009876.1200%" type="PERCENT">
</fmt:parseNumber>
<h1>${num1}</h1>
<fmt:parseNumber var="num2" value="$009876.987600" type="CURRENCY" parseLocale="en_US">
</fmt:parseNumber>
<h1>${num2}</h1>
<fmt:parseNumber var="num3" value="$009876.987600"
                 pattern="$#" integerOnly="true">
</fmt:parseNumber>
<h1>${num3}</h1>
</body>
</html>
```

3. `<fmt:formatDate>`:使用指定的风格或者模式格式化日期和时间
   * 属性:
     * var:变量名
     * value:一边填`<%= new Date() %>`
     * scope:作用域
     * type:显示那些部分
       * date:只显示年月日
       * time:只显示时分秒
       * both:都显示
     * dateStyle:设置年月日部分的表现形式
       * DEFAUTL:`2022-3-7`
       * LONG:`2022年3月7日`
       * SHORT:`22-3-7`
       * FULL:`2022年3月7日 星期一`
     * timeStyle:设置时分秒部分的表现形式
       * DEFAULT:`19:39:35`
       * LONG:`下午07时39分53秒`
       * SHORT:`下午7:40`
       * FULL:`下午07时40分24秒 GMT+08:00`
     * pattern:自定义格式化属性
       * y:年
       * M:月
       * d:日
       * H:小时（24小时制）
       * h:小时（12小时制）
       * m:分钟
       * s:秒
       * S:毫秒
       * E:星期几
       * D:一年中的第几天
       * w:一年中的第几周
       * W:一个月中的第几周
       * a:上午还是下午
       * k:一天中第几个小时（24小时制）
       * K:一天中第几个小时（12小时制）

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<%@ page import="java.util.Date" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<fmt:formatDate value="<%=new Date()%>" var="date" scope="page"
                type="both" dateStyle="FULL" timeStyle="FULL"/>
<h1>${date}</h1>

<fmt:formatDate value="<%=new Date()%>" var="date1" scope="page"
                pattern="yyyy年MM月dd日 E HH:mm:ss:SSS 一年中第D天 一年中第w周 一个月中第W周 a第K个小时 一天中第k个小时"/>
<h1>${date1}</h1>
</body>
</html>
```

4. `<fmt:parseDate>`解析时间字符串，把指定的日期字符串解析成国际标准时间
   * 属性:
     * var
     * value:日期字符串
     * scope
     * type
     * dateStyle
     * timeStyle
     * pattern

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<%@ page import="java.util.Date" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<fmt:parseDate var="date" value="2025/2/27 13:21:31:125"
               pattern="yyyy/MM/dd HH:mm:ss:SSS"/>
<h1>${date}</h1>
<%--显示:Thu Feb 27 13:21:31 GMT+08:00 2025--%>

</body>
</html>
```

# JSTL函数标签
* JSP页面加入以下代码:
* `<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>`

1. fn:contains(s1, s2):测试输入字符串是否包含指定字符
   * s1:输入字符串
   * s2:指定字符
2. fn:containsIgnoreCase(s1, s2):测试输入字符串是否包含指定字符,对大小写不敏感。
3. fn:startsWith():字符串是否以指定的前缀开始
4. fn:endsWith():字符串是否以指定的后缀结束
5. fn:escapeXml():不跳过可以作为XML标记的字符
   * 正常输出是会自动跳过字符串中的`<xxx>`这些内容
   * 使用该方法输出则不会跳过
6. fn:indexOf(s1, s2):返回指定字符串在输入字符串中出现的位置
   * s1输入字符串
   * s2指定字符串
   * 没有则输出-1
7. fn:join(arr, s1):将数组中的元素合成一个字符串后输出
   * arr:字符串数组（或者是元素可以强转成字符串的数组），不能用List
   * s1:元素之间的分隔符，可以位空字符串
8. fn:length():返回数组长度
9. fn:replace(s1,s2,s3):将字符串中指定的位置替换成指定的字符串然后返回
   * s1:整体字符串
   * s2:被替换的字符串
   * s3:替换的字符串
10. fn:spllit():将字符串按指定的分割符分成字符串数组后返回
11. fn:substring(s,i1,i2):返回字符串的子集
    * s:字符串
    * i1:起始位置
    * i2:终止位置
    * 包前不包后
12. fn:substringAfter(s1,s2):返回字符串在指定字符串之后的子集
    * s1:整体字符串
    * s2:指定字符串
13. fn:substringBefore():返回字符串在指定字符串之前的子集
14. fn:toLowerCase():转小写
15. fn:toUpperCase():转大写
16. fn:trim():移除首位空格

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<c:if test="${fn:containsIgnoreCase('HelloWord', 'LL')}">
    <h1>HelloWord包含ll</h1>
</c:if>

<c:set var="s1" value="HelloWord"/>

<h1>${s1}以He开头:${fn:startsWith(s1, "He")}</h1>
<h1>${s1}以ord结尾:${fn:endsWith(s1, "ord")}</h1>

<c:set var="xml" value="he<abc>llo</abc>wo<ad>rd"/>
<h1>${xml}</h1><!--输出helloword-->
<h1>${fn:escapeXml(xml)}</h1><!--输出he<abc>llo</abc>wo<ad>rd-->

<h1>位置:${fn:indexOf(s1, "Wo")}</h1><!--输出5-->

<%
    int[] list = new int[]{1,2,3,4,5,6};
    request.setAttribute("list", list);
%>
<h1>${fn:join(list, ",")}</h1><!--输出1,2,3,4,5,6-->

<h1>${s1}的长度是:${fn:length(s1)}</h1>

<h1>${s1}替换后:${fn:replace(s1, "o", "abcd")}</h1><!--HellabcdWabcdrd-->

<c:set var="s2" value="Hello,word,!!!,it,is,so,great"/>
<c:forEach items="${fn:split(s2, ',')}" var="s" >
    <h1>分割后:${s}</h1>
</c:forEach>

<h1>${fn:substring(s1, 2, 3)}</h1><!--l-->

<c:set var="s3" value="oW"/>

<h1>${s1}在${s3}之后的子集:${fn:substringAfter(s1, s3)}</h1><!--ord-->
<h1>${s1}在${s3}之前的子集:${fn:substringBefore(s1, s3)}</h1><!--Hell-->
</body>
</html>
```




