
C\# 13 即 .Net 9 按照计划会在2024年11月发布，目前一些新特性已经定型，今天让我们来预览其中的一个新特性：


作者注：该特性虽然随着 C\# 13 发布，但是仍然是处于 preview 状态的特性，请谨慎使用


## 半自动属性 Semi\-auto properties


大家都知道，C\# 早在 3\.0 时候就添加了[自动属性](https://github.com)这个特性，让我们一起来回顾一下自动属性 :



```
public string Name { get; set; }

```

以上代码等价于下面的全手动实现的属性：



```
private string _name;

public string Name
{
    get
    {
        return _name;
    }
    set
    {
        _name = value;
    }
}

```

然后在C\# 6\.0 中，支持了 lambda 表达式来稍微简化一下



```
private string _name;

public string Name
{
    get => _name;
    set => _name = value;
}

```

可以看到，自动属性这个特性极大的方便了属性的书写，省略了大量的繁琐代码，手动属性需要自己手动声明属性背后对应的私有字段。


但是，当我们需要在获取或设置属性时自定义一些逻辑时，自动属性就无能为力了，只好退化成手动实现的属性，例如



```
 private string _name;

 public string Name
 {
     get => _name;
     set => _name = "Hello " + value;
 }

```

现在，C\# 13 通过引入 field 关键字来解决这个问题，在允许自定义逻辑的基础上，可以不必自行声明对应的字段。


以上代码可以写成：



```
public string Name
{
    get => field;
    set => field = "Hello " + value;
}

```

进一步的，我们可以把 get 改成全自动属性的写法，也是支持的



```
public string Name
{
    get;
    set => field = "Hello " + value;
}

```

可以看到，半自动属性将极大的方便我们在拥有自定义逻辑字段上的代码编写。提高了代码的可读性，以及避免了字段名称的污染。


题外话，field 是一个上下文关键字，只在属性代码块中有效，例如在方法中使用 field 将不产生任何效果



```
public string Test()
{
    return field; // ERROR: The name 'field' does not exist in the current context
}

```

## 破坏性更新


需要注意的是，引入这个 field 关键字是一个破坏性更新，例如你的原有代码：



```
public int Age
{
    get
    {
        var field = 18;
        return field;
    }
}

```

原有代码里存在以 field 为名字的变量的话，当升级到 C\# 13 时将改变行为，现在这个属性将返回 field 默认值" **0"** 而不是期望的 "**18 "**


你需要进行如下修改以避免变量 field 被识别为关键字



```
public int Age
{
    get
    {
        var field = 18;
        return @field;
    }
}

```

## 结尾


这个特性经过数次的易稿，field 关键字也经过数次变更才最终确定，终于在今年推出了。


看起来一个简单的功能，但是在背后有千丝万缕的关系，以及存在的破坏性更新，可以看出 C\# 团队在推出功能时需要考虑及照顾的东西有很多，所以速度难免会被拖慢。


最后，当前这个特性已经在 Visual Studio 2022 17\.12 Preview 3\.0 中实装，需要把 C\# 语言版本设置为preview，大家可以自行尝试一下。


## 参考


[csharplang/proposals/field\-keyword.md at main · dotnet/csharplang](https://github.com):[MeoMiao 萌喵加速](https://biqumo.org)


[Proposal: `field` keyword in properties · Issue \#140 · dotnet/csharplang](https://github.com)


