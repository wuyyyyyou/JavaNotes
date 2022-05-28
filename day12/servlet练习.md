# 实现加法器

* 这里action注意一点：写全IP，如果写成`/d08/t1`则会补全为`http://localhost:8080/d08/t1`,如果写成`da08/t1`会自动补全为`http://localhost:8080/pages/day08/d08/t1`
* 也可以写成`/webDemo_1/d08/t1`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        var sub = function () {
            var form = document.getElementById("f_01");
            console.log(form.getAttribute("action"));
            form.submit();
        }
    </script>
</head>
<body>
<form action="http://localhost:8080/webDemo_1/d08/t1" method="post" id="f_01">
    <input type="text" name="x"> + <input type="text" name="y">
    <br>
    <input type="button" onclick="sub()" value="计算">
</form>
</body>
</html>
```

```java
@WebServlet(name = "day08.t1", value = "/d08/t1")
public class Test1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String x = req.getParameter("x");
        String y = req.getParameter("y");

        double res = Double.parseDouble(x) + Double.parseDouble(y);

        resp.getWriter().write("<html><body><h1>" + x + "+" + y + "=" + res + "</h1></html></body>");
    }
}
```

# 实现注册和登录功能
## 注册
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        var sub = function () {
            var f0 = document.getElementById("f_0");
            var username = document.getElementById("username").value;
            var psd = document.getElementById("psd").value;
            var rpsd = document.getElementById("rpsd").value;

            //用户名要求5-10位数字或者字母
            if (!/^(\d|[a-z]|[A-Z]){5,10}$/.test(username)) {
                alert("用户名非法!");
                return false;
            //密码要求8-16位任意字符
            } else if (!/^\w{8,16}$/.test(psd)) {
                alert("密码非法!");
                return false;
            } else if (psd !== rpsd) {
                alert("两次输入密码不相同!");
                return false
            } else {
                f0.submit();
            }
        };

    </script>
</head>
<body>
<form action="/webDemo_1/d08/t2" method="post" id="f_0">
    <table>
        <tr>
            <td>用户名:</td>
            <td><input type="text" name="username" id="username"></td>
        </tr>
        <tr>
            <td>密码:</td>
            <td><input type="password" name="psd" id="psd"></td>
        </tr>
        <tr>
            <td>再次输入密码:</td>
            <td><input type="password" name="rpsd" id="rpsd"></td>
        </tr>
        <tr>
            <td><input type="button" value="注册" onclick="sub()"></td>
            <td><input type="reset" value="重置"></td>
        </tr>
    </table>
</form>
</body>
</html>
```

```java
@WebServlet(name = "day08.t2", value = "/d08/t2")
public class Test2 extends HttpServlet {
    public static Map<String, String> users = new HashMap<>();
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置这个编码，解决response.getWriter().write输出中文乱码问题
        resp.setCharacterEncoding("gbk");

        String username = req.getParameter("username");
        String psd = req.getParameter("psd");

        if (users.containsKey(username)) {
            resp.getWriter().write("<html><body>用户名:" + username + "已存在!<html><body>");
        } else {
            users.put(username, psd);
            System.out.println(username + "," + psd);
            resp.getWriter().write("<html><body>用户名:" + username + "注册成功!<html><body>");
        }
    }
```

## 登录

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        var sub = function () {
            var f0 = document.getElementById("f_0");
            var username = document.getElementById("username").value;
            var psd = document.getElementById("psd").value;

            //用户名要求5-10位数字或者字母
            if (!/^(\d|[a-z]|[A-Z]){5,10}$/.test(username)) {
                alert("用户名非法!");
                return false;
                //密码要求8-16位任意字符
            } else if (!/^\w{8,16}$/.test(psd)) {
                alert("密码非法!");
                return false;
            } else {
                f0.submit();
            }
        };
    </script>
</head>
<body>
<form action="/webDemo_1/d08/t3" method="post" id="f_0">
    <table>
        <tr>
            <td>用户名:</td>
            <td><input type="text" name="username" id="username"></td>
        </tr>
        <tr>
            <td>密码:</td>
            <td><input type="password" name="psd" id="psd"></td>
        </tr>
        <tr>
            <td><input type="button" value="登录" onclick="sub()"></td>
            <td><input type="reset" value="重置"></td>
        </tr>
    </table>
</form>
</body>
</html>
```

```java
@WebServlet(name = "day08.Test3", value = "/d08/t3")
public class Test3 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setCharacterEncoding("gbk");
        String userName = req.getParameter("username");
        String psd = req.getParameter("psd");

        if (Test2.users.containsKey(userName)) {
            if (Objects.equals(Test2.users.get(userName), psd)) {
                resp.getWriter().write("<html><body>用户名:" + userName + "登录成功!<html><body>");
            } else {
                resp.getWriter().write("<html><body>用户名:" + userName + "密码错误!<html><body>");
            }
        } else {
            resp.getWriter().write("<html><body>用户名:" + userName + "不存在!<html><body>");
        }
    }
}
```

# 构建书籍列表，点击书名查看书籍详情
* 首先有书籍列表的数据,java写
* 通过servlet+书籍列表数据构建一个书籍列表页面
* 点击查看详情

* 注意：Java逻辑和组装页面逻辑相分离的原则

# Servlet的生命周期

### 生命周期：
* 从生到死


### javaSE：
* 手动new对象，main方法中调用这个对象，main方法执行完毕，程序结束，对象销毁
* 我们在使用servlet，没有new对象（或者手动通过反射实例化对象）
* servlet生命周期由tomcat控制

### 四个生命周期
* 1.创建：第一次使用servlet由Tomcat调用*构造方法*创建，只调用一次，**servlet在tomcat服务器中是单实例的**。
* 2.初始化:servlet对象创建之后调用*init方法*，只调用一次，通常我们直接使用父类的init就可以。
* 3.服务:每次请求servlet的时候都会调用*service方法*，由该方法决定调用doGet还是doPost。
* 4.销毁:Tomcat停止或者这个servlet所在的web项目被重新部署时调用*destroy方法*，只调用一次。

<br>

* 四个生命周期指的是一次Tomcat的启动和停止，如果再次启动就是新的生命周期了。

# Servlet线程安全问题
* **Servlet是单实例多线程的**
  * 每次请求servlet都会创建一个新的线程，这次请求终止时请求创建的线程就会被销毁
  * 但是这多个线程操作的是一个servlet对象
  * servlet中普通的成员变量都会在多个请求线程中被使用
  * **所以存在线程安全问题**

## 避免冲突:
* 尽量不要在servlet类中使用成员变量：
  * 如果确实需要使用成员变量：
    * 自己封装一个普通java类，在doget或者dopost中new一个实例来使用。
* 如果非要使用成员变量，就要给使用到成员变量的代码块加上代码锁，并要尽量缩小同步锁的范围（不推荐）。
  * 加上了代码锁降低多线程的执行效率，能不用尽量不要用

# Servlet自动加载
* 假设init方法要消耗大量的时间，用户在第一次访问的时候Tomcat会构造对象并执行init方法，会耗时很久
* 对于web应用，请求应该限制在毫秒级。

<br>

* 在web.xml中设置load-on-startup可以在servlet可以让Tomcat服务器在启动的时候就创建servlet对象并且执行init方法。
  * load-on-startup内容是一个正整数,数字越小，创建对象的优先级越高，1有最高的优先级.
  * 也可以在注解@WebServlet中设置loadOnStartup属性，默认值为-1（不在Tomcat启动时创建）

```html
<servlet>
        <servlet-name>testHello</servlet-name>
        <servlet-class>com.test.servlet.TestHello</servlet-class>
        <load-on-startup>1</load-on-startup>
</servlet>
```