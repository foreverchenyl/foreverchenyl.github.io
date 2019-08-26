---


title: C#, .NET
date: 2019-07-13 16:59:03
categories:
- 后端
tags:
- .Net
---

C#       .NET汇总 ~~~~

<!-- More -->

### 一、private、 protected、 public、internal 修饰符的访问权限

private : 私有成员, 在类的内部才可以访问(只能从其声明上下文中进行访问)。  

protected : 保护成员，该类内部和从该类派生的类中可以访问。 

public : 公共成员，完全公开，没有访问限制。  

internal: 在同一命名空间内可以访问。



### 二、ASP.NET 页面之间传递值的几种方式

- 使用QueryString, 如…?id=1; response. Redirect()…

- 使用Session变量

- 使用Server.Transfer

  

### 三、实现多态的过程中 overload 重载 与override 重写的区别

override 重写与 overload 重载的区别：

重载是方法的名称相同。不同的参数类型，不同的参数个数，不同的参数顺序，进行多次重载以适应不同的需要。

Override 是进行基类中函数的重写，实现多态。

- **总结**

override（重写）

　　 1、方法名、参数、返回值相同。

　　 2、子类方法不能缩小父类方法的访问权限。

　　 3、子类方法不能抛出比父类方法更多的异常(但子类方法可以不抛出异常)。

　　 4、存在于父类和子类之间。

　　 5、方法被定义为final不能被重写。

　overload（重载）

　　1、参数类型、个数、顺序至少有一个不相同。 

　　2、不能重载只有返回值不同的方法名。



### 四、编程实现一个冒泡排序算法

```c#
int[] scores = new int[] { 3,11,15, 5, 6, 7, 9 };
for (int i = 0; i < scores.Length-1; i++)
{
    for (int j = 0; j < scores.Length-1-i; j++)
    {
        if (scores[j]>scores[j+1])
        {
            int item = scores[j];//存储最大值
            scores[j] = scores[j+1];//最前面的是最小值
            scores[j + 1] = item;//放在数组的最后
        }
    }
}

for (int i = 0; i < scores.Length; i++)
{
    Console.WriteLine(scores[i]);
}
Console.ReadLine();
 
输出结果是：3,5,6,7,9,11,15
//每循环一次都有一个最大值放在数组的最后
    
--------------------------------------------------------------------   
    
int [] array = new int [*] ; 
int temp = 0 ; 
for (int i = 0 ; i < array.Length - 1 ; i++) 
{ 
	for (int j = i + 1 ; j < array.Length ; j++) 
    { 
        if (array[j] < array[i]) 
        { 
            temp = array[i] ; 
            array[i] = array[j] ; 
            array[j] = temp ; 
        } 
	} 
}
```

### 五、递归算法实现

一列数的规则如下: 1、1、2、3、5、8、13、21、34…… 求第30位数是多少，用递归算法实现：

```c#
public int Foo(int i) 
{ 
    if (i <= 0) 
    {
         return 0; 
    }
    else if(i > 0 && i <= 2) 
    {
    	return 1; 
    }
    else
    {
       return Foo(i -1) + Foo(i - 2);  
    }
} 
```



### 六、装箱和拆箱

装箱是将值类型转换为引用类型 ；拆箱是将引用类型转换为值类型 
利用装箱和拆箱功能，可通过允许值类型的任何值与Object 类型的值相互转换，将值类型与引用类型链接起来 
例如： 
int val = 100; 
object obj = val; 
Console.WriteLine (“对象的值 = {0}", obj); 
这是一个装箱的过程，是将值类型转换为引用类型的过程 

int val = 100; 
object obj = val; 
int num = (int) obj; 
Console.WriteLine ("num: {0}", num); 
这是一个拆箱的过程，是将值类型转换为引用类型，再由引用类型转换为值类型的过程   

- 值类型只会在栈中分配。引用类型分配内存与托管堆。

  [详细](https://blog.csdn.net/qiaoquan3/article/details/51439726)
  
  

### 七、String

- string str = null 是不给他分配内存空间,而string str = “” 给它分配长度为空字符串的内存空间。
  


### 八、WebService与WCF

使用XML扩展标记语言来表示数据，WSDL来实现服务接口相关的描述。WCF：其实一定程度上就是ASP.NET Web Service, 因为它支持Web Service的行业标准和核心协议，因此ASP.NET Web Service和WSE能做的事情，它几乎都能胜任，跨平台和语言更不是问题（数据也支持XML格式化，而且提供了自己的格式化器）。
但是WCF作为微软主推一个通讯组件或者平台，它的目标不仅仅是在支持和集成Web Service,因为它还兼容和具备了微软早期很多技术的特性。

#### 1、WCF和ASMX WebService的区别

最基本的区别在于，ASMX或者ASP.NET WebService是用来通过基于HTTP的SOAP来实现通讯。但WCF可以使用任意协议（HTTP,TCP/IP,MSMQ,NamedPipes等），消息封装可以使用任意格式（默认SOAP）。

#### 2、WCF的Service EndPoints

对于WCF服务来说，Endpoints暴漏了其被调用的方式；客户端必须知道这些细节才能够与服务端进行通讯。每个Endpoints就是用于通信的入口，客户端和服务端通过Endpoint交换信息。
一个WCF Service Endpoint一般包括3个基本的元素：

- Address：定义了“WHERE”，一串URL标识了服务的地址；
- Binding：定义了“HOW”，确定服务器怎么被访问，例如消息发送的传输协议（如TCP，HTTP），安全（如SSL，SOAP消息安全）。
- Contract：定义了“WHAT”，即服务提供的内容和契约方式，描述的是消息所包含的内容，以及消息的组织和操作方式，例如是one-way,duplex和request/reply。

- 托管WCF Service

  1. 托管程序或者自我托管[Managed Application/ Self Hosting]：
     - Console Application
     - Windows Application
     - Windows Service

  2. Web Server托管

  - IIS 6.0 (ASP.NET Application supports only HTTP)
  - Windows Process Activation Service (WAS) i.e. IIS 7.0 supports HTTP, TCP, NamedPipes, MSMQ.

  

- **WCF Service中启用操作重载（Operation Overloading** **）？**

默认情况下，WSDL不支持操作重载，重载行为必须通过OperationContract的Name属性来实现。如下

> ```
>1: [ServiceContract]
> 2: interface IMyCalculator
>    3: {
>    4:    [OperationContract(Name = "SumInt")]
>    5:    int Sum(int arg1,int arg2);
>    6:  
>    7:    [OperationContract(Name = "SumDouble")]
>    8:    double Sum(double arg1,double arg2);
>    9: }
>    ```
>    

这些代码最终会转换成SumInt和SumDouble两个方法。



- **WCF 信息交换模式（Message Exchange Patterns ）**

  a.请求/相应模式：

  作为默认的MEP，该模式在服务操作被调用同时，向请求者发送回应消息，及时是void类型，也会返回一个空的SAOP包。

  b.单工模式：

  在某些情况下，我们需要调用服务执行部分特定逻辑，但不需要接受任何反馈，此时我们需要使用单工模式。如果我们需要消息排队，单工模式就是唯一的选择。

  c.双工模式：

  双工模式简单的说就是双向的信息通道。适合于需要发送消息启动长期运行的进行，并在执行完毕后发回通知的情况。



- **WCF中的安全模式**

  在WCF中，我们可以在不同的级别上定义安全性配置：

  **a.传输层安全**

  传输层安全需要考虑消息在物理链路上的完整性、私密性和认证，它依赖于Binding方式，因为大部分的Binding本身都包含了内置的安全处理。

  ```
  1: <netTcpBinding>
  2: <binding name="netTcpTransportBinding">
  3:    <security mode="Transport">
  4:               <Transport clientCredentialType="Windows" />
  5:    </security>
  6: </binding>
  7: </netTcpBinding>
  ```

  **b.消息层安全**

  消息层安全通常是指消息的加密。

  ```
  1: <wsHttpBinding>
  2: <binding name="wsHttpMessageBinding">
  3:   <security mode="Message">
  4:               <Message clientCredentialType="UserName" />
  5:   </security>
  6:  </binding>
  7: </wsHttpBinding>
  ```

  这种安全性通常依赖于需求，但是我们可以使用一种复合的安全模式，如下：

  ```
  1: <basicHttpBinding>
  2: <binding name="basicHttp">
  3:   <security mode="TransportWithMessageCredential">
  4:              <Transport />
  5:                   <Message clientCredentialType="UserName" />
  6:   </security>
  7: </binding>
  8: </basicHttpBinding>
  ```



### 九、C#中的委托

委托可以把一个方法作为参数代入另一个方法。 委托可以理解为指向一个函数的引用。
是，是一种特殊的委托。



### 十、反序列化的使用

如果XML是由类型序列化得到那的，那么反序列化的调用代码是很简单的，
反之，如果要面对一个没有类型的XML，就需要我们先设计一个（或者一些）类型出来，
这是一个逆向推导的过程，请参考以下步骤：

1. 首先要分析整个XML结构，定义与之匹配的类型
2. 如果XML结构有嵌套层次，则需要定义多个类型与之匹配
3. 定义具体类型（一个层级下的XML结构）时，请参考以下表格。

| XML形式      |                                                     处理方法 | 补充说明             |
| ------------ | -----------------------------------------------------------: | -------------------- |
| XmlElement   |                                                 定义一个属性 | 属性名与节点名字匹配 |
| XmlAttribute |                                    [XmlAttribute] 加到属性上 |                      |
| InnerText    |                                         [XmlText] 加到属性上 | 一个类型只能使用一次 |
| 节点重命名   | 根节点：[XmlType("testClass")] 元素节点：[XmlElement("name")] 属性节点：[XmlAttribute("id")] 列表子元素节点：[XmlArrayItem("Detail")] 列表元素自身：[XmlArray("Items")] |                      |

- 默认情况下，类型的所有公开的数据成员（属性，字段）在序列化时都会被输出， 如果希望排除某些成员，可以用**[XmlIgnore]**来指出

[More](https://www.cnblogs.com/fish-li/archive/2013/05/05/3061816.html)



### 十一、Entity Framework

微软官方提供的ORM工具，ORM让开发人员节省数据库访问的代码时间，将更多的时间放到业务逻辑层代码上。EF提供变更跟踪、唯一性约束、惰性加载、查询事物等。开发人员使用Linq语言，对数据库操作如同操作Object对象一样省事。

O/RM是什么？

​    ORM 是将数据存储从域对象自动映射到关系型数据库的工具。ORM主要包括3个部分：域对象、关系数据库对象、映射关系。ORM使类提供自动化CRUD，使开发人员从数据库API和SQL中解放出来。



#### 1、 EF有三种使用场景：

- 从数据库生成Class （DBFirst）

- 由实体类生成数据库表结构 （Code Firs）

- 通过数据库可视化设计器设计数据库，同时生成实体类。（Model First）

  

#### 2、**DBContext**

![](/images/entityframework.png)



#### 3、使用查询

三种查询方式 1) LINQ to Entities,  2) Entity SQL,  and 3) Native SQL

**LINQ to Entities：**

LINQ Method syntax:

```c#
//Querying with LINQ to Entities 
using (var context = newSchoolDBEntities())
{
	var L2EQuery = context.Students.where(s => s.StudentName == "Bill");
	var student = L2EQuery.FirstOrDefault<Student>();
}

//LINQ Query syntax:
using (var context = new SchoolDBEntities())
{
	var L2EQuery = from st in context.Students
	where st.StudentName == "Bill"select st;
	var student = L2EQuery.FirstOrDefault<Student>();
}
```



**Entity SQL:**

```c#
//Querying with Object Services and Entity SQL

string sqlString = "SELECT VALUE st FROM SchoolDBEntities.Students " +
                    "AS st WHERE st.StudentName == 'Bill'";

var objctx = (ctx as IObjectContextAdapter).ObjectContext;
ObjectQuery<Student> student = objctx.CreateQuery<Student>(sqlString);
Student newStudent = student.First<Student>();

//使用EntityDataReader
using (var con = newEntityConnection("name=SchoolDBEntities"))
{
	con.Open();
	EntityCommand cmd = con.CreateCommand();
	cmd.CommandText = "SELECT VALUE st FROM SchoolDBEntities.Students as st where                                st.StudentName='Bill'";

    Dictionary<int, string> dict = newDictionary<int, string>();
    using (EntityDataReader rdr = cmd.ExecuteReader(CommandBehavior.SequentialAccess | CommandBehavior.CloseConnection))
    {
        while (rdr.Read())
        {
            int a = rdr.GetInt32(0);
            var b = rdr.GetString(1);
            dict.Add(a, b);
        }
    }
}
```



**Native SQL:**

```c#
using (var ctx = newSchoolDBEntities())
{
	var studentName = ctx.Students.SqlQuery(
     "Select studentid, studentname, standardId from Student where studentname='Bill'"   
    ).FirstOrDefault<Student>();
}
```
