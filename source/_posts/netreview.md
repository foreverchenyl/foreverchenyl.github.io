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

