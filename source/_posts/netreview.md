---


title: C#, .NET所遇问题汇总
date: 2019-08-26 16:59:03
categories:
- 后端
tags:
- .Net
---

汇总C#       .NET工作中遇到的一些方法的使用 ~~~~

<!-- More -->

### 一、Distinct用法

今天遇到Ticket System模块的Attribute属性列表相同属性值显示重复的问题，涉及到Distinct用法。

![](/images/repeatfilter.png)



c#中的Distinct大多用于对数组去重，一般数组为基础的数据类型，例如 int,string.  --> .Distinct().ToList()

也可以用于对象去重。

![](/images/Distinct.png)



**Distinct方法不加参数的话，去重的规则是比较对象集合中对象的引用是否相同，如果相同，则去重，否则不去重。** 

问题涉及一个AttributeFilter对象：

```c#
public class AttributeFilter
{	
    public string Property { get; set }   
    public string Value { get; set } 
}
```

AttributeFilter对象里的Property和Value都**不相同**的对象，我们才算做不重复的对象，要输出集合中不重复的对象，这时，我们需要用到了Distinct的第二个方法，方法要求传入的参数是IEqualityComparer类型，继承一个泛型接口，继承IEqualityComparer接口必须实现Equals和GetHashCode方法：

```c#
public class AttributeComparer : IEqualityComparer<AttributeFilter>
{
	public bool Equals(AttributeFilter x, AttributeFilter y)
	{
		return (x.Property == y.Property) && (x.Value == y.Value);
	}

    public int GetHashCode(AttributeFilter obj)
    {
        return obj.ToString().ToLower().GetHashCode();
    }
}
```

在使用这个参数过滤：

```c#
AttributeFilters.Distinct(new AttributeComparer()).ToList();
```

Result:

![](/images/filterresult.bmp)



[C# Distinct将对象按条件去重](https://blog.csdn.net/lishuangquan1987/article/details/76096022)



### 二、CORS跨域

YTWO App调用web后端API存在的跨域问题：

采用：webapi使用System.Web.Http.Cors配置跨域访问

在webapi中使用System.Web.Http.Cors配置跨域信息可以有**两种方式。** 
1) 一种是在App_Start.WebApiConfig.cs的Register中配置如下代码，**这种方式将在所有的webapi Controller里面起作用。**

eg:

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web.Http;
using System.Web.Http.Cors;

namespace YTWO.Service
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API 配置和服务

            // Web API 路由
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{action}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            //这是重点，从配置文件的appsettings节点中读取跨域的地址
            var cors = new EnableCorsAttribute(ConfigurationManager.AppSettings["origins"], "*", "*");
            config.EnableCors(cors);
        }
    }
}
```

配置文件如下，**注意一定要加上http**

```xml
<add key="origins" value="http://localhost:9012,http://192.168.1.108:9012" />
```



2) **第二种方式就是在每个webapiController类中设置，即每个控制器个性化配置。**

eg:

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Http.Cors;
using System.Web.Mvc;

namespace Service.Controllers
{
    // 允许指定域访问API
    [EnableCors("http://localhost:9012,http://192.168.1.108:9012", "*", "*")]
    public class HomeController : Controller
    {
        public ActionResult Index()
        {
            ViewBag.Title = "Home Page";

            return View();
        }
    }
    
    // 允许所有域访问API
    [EnableCors("*", "*", "*")]
    public class CompanyController : Controller
    {
        public ActionResult Index()
        {
            ViewBag.Title = "Company Page";

            return View();
        }
    }
}

```

