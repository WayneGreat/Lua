

# Lua Learning



### 1、输出命令行参数

```lua
for i = -2, #arg do
    print(i, arg[i])
end
```

```bash
$ lua args.lua hello 123 world 456
-2      nil
-1      lua
0       args.lua
1       hello
2       123
3       world
4       456

# 下标从1开始，没有就是nil
# 块的分界符为do  end
# 语句不用加分号
# #arg为全局变量
```



### 2、变量

```lua
print(wangdao)
wangdao = "biancheng"
do
    -- wangdao = "wangdao" 所有的变量默认是全局变量
    -- local wangdao = "wangdao"
    local wangdao = wangdao --用 全局变量的值对局部变量赋值
    print(wangdao)
end
print(wangdao)
```



### 3、Lua数据类型

#### nil类型

#### boolen类型

```lua
print(0 and 123)        -- 123
print(nil and 123)      -- nil
print(false and 123)    -- false
print(123 or nil)       -- 123
print(false or nil)     -- nil 
print(not nil)          -- true
print(not 0)            -- false
```



#### 数值类型

- 与数学计算过思维一致，+ - * / ^



#### 字符串类型

```lua
print("I'm strong")
print("\"how are you\" she said")
print('"how are you" she said')
str = [["I am 18", she' said]]
print(str)
code = [==[a[b[i]] = 1]==]
print(code)
print(#code)
print("how are you "..str)
```

```bash
$ lua string.lua 
I'm strong
"how are you" she said
"how are you" she said
"I am 18", she' said
a[b[i]] = 1
11
how are you "I am 18", she' said
```

- **`table.concat (table [, sep [, i [, j]]])`**
  - 给定一个所有元素都是字符串或数字的数组，返回  `table[i]..sep..table[i+1] ··· sep..table[j]`。 `sep` 的默认值是空字符串，`i`默认为是1， `j` 默认为是表的长度。如果  `i` 大于  `j`，返回空字符串

- 两种字符串的差距

```lua
-- 第一种写法 good
local time_beg = os.clock()
local str = ""
local t = {}
for i = 1,300000 do
    t[#t+1] = 'a'
end
str = table.concat(t) -- 连接数组的每一个元素
-- print(str)
local time_end = os.clock()
print(time_end - time_beg)

-- 第二种写法 bad
local time_beg = os.clock()
local str  = ""
for i = 1,300000 do
    str = str .. 'a'
end
local time_end = os.clock()
print(time_end - time_beg)
```

```bash
$ lua bigstring.lua 
0.065777
16.083881
```

- 字符串标准库

| 库函数         | 效果                       |
| -------------- | -------------------------- |
| string.len     | 获取字符串长度             |
| string.upper   | 将字母转换成大写           |
| string.lower   | 将字母转换成小写           |
| string.rep     | 重复字符串，构造新的字符串 |
| string.reverse | 翻转字符串                 |
| string.sub     | 子串                       |
| string.byte    | 获取串中字符的ASCII码      |
| string.char    | 根据ASCII码，转成字符      |
| string.format  | 格式化输出                 |

 **`string.format (formatstring, ···)`**

- 使用 string.format 可以实现格式化输出。 format 函数的效果与C语言的 printf 函数几乎一致，只有
  一些稍微的区别：format函数不支持 * , l 和 p 等控制符。除此以外，因为 format 函数的本质是调用
  了C当中的 printf 函数，而C语言的字符串是以'\0'结尾的，所以如果Lua风格的字符串当中有'\0'字符，
  就不能使用 s 控制符，为此， format 函数引入了 q 控制符来显示。
- 总之， string.format 函数支持 c , d , e , f , g , i , o , u 和 x 等运算符。其中 s 和 q 可以用来控制
  字符串的显示。  

```lua
local str = "wangdao"
print(string.byte(str,1,-1))
print(string.char(119,97,110,103,100,97,111))
print(string.len("wangdao"))
print(string.upper("wangdao"))
print(string.lower("HELLOniHAo"))
print(string.rep("nihao",3))
print(string.reverse("evil"))
print(string.sub("however",1,3))
local str2 = "a\0b"
print(string.format("luaStrs = %s, luaStrq = %q",str2,str2))
```

```bash
$ lua stringLib.lua 
119     97      110     103     100     97      111
wangdao
7
WANGDAO
hellonihao
nihaonihaonihao
live
how
luaStrs = a, luaStrq = "a\000b"
```

**一般不使用lua的find。。。模式匹配，常用pcre代替**



#### 表类型

- 唯一的数据结构（由键值对构成的集合）

- 空表 {}    查找[] (等价于 .运算符，后接符合标识符规范)   修改[]=
- **规范：尽量不在[]中写入字符串，尽量使用.来用字符串值作为键**
- 如果表的键全部都是正整数（不含0），那么这个表称为数组
  - 空洞：数组中间有值为nil
  - 序列：无空洞（#）
- 如果表的键全部都是字符串类型，那么这个表称为记录
- 表构造器
  - 列表式：直接在列表中写出值，键默认从1开始
  - 记录式：{键 = 值。。。}
  - 混合式

```lua
local t = {
    len = 3,
    1, -- 只有值的内容，会自动从1开始编号
    2,
    3,
    ["1"] = 4,
    -- [3] = 5, 一旦发生冲突，忽视键=值版本的
    [100] = 5,
    {x = 1, y = 2}
}
print(t.len)
print(t[1])
print(t[3])
print(t[100])
print(t[4].x)
```

```bash
$ lua mix.lua 
3
1
3
5
1
```



#### 函数类型

```lua
function f(arg)
    return arg
end
print(f(1))
print(f("hello"))
g = f -- 函数是一等公民，可以像普通值 一样作为右值
print(g(2))
local object = {}
object.attack = 50
object.func = function (self) print(self.attack) end
object.func(object)
object:func() -- :运算符默认添加一个self参数，相当于给func传递一个self参数
```

```bash
$ lua func.lua 
1
hello
2
50
50
```

- 函数的使用【快排】
```lua
function Sort(arr,left,right)
    if left < right then
        local pivot = Partition(arr,left,right)
        Sort(arr,left,pivot-1)
        Sort(arr,pivot+1,right)
    end
end
function Partition(arr,left,right)
    local j = left
    local pivot = arr[right]
    for i = left,right-1 do
        if arr[i] < pivot then
            arr[i],arr[j] = arr[j],arr[i]
            j = j + 1
        end
    end
    arr[j],arr[right] = arr[right],arr[j]
    return j;
end
local list = {1,3,5,7,9,2,4,6,8,10}
Sort(list,1,#list)
for i,v in ipairs(list) do
    print(i,v)
end
```

- 可变参数的使用（select函数）
- `select (index, ···)`
  -  index是一个数字：从第index个参数返回
  -  index是一个"#"，返回可变参数个数

```lua
x,y,z = select(3,1,2,3,4,5) -- 返回第三个（包括第三个）之后的所有参数
print(x,y,z)
x = select("#",1,2,3,4,5,6,7)
print(x)

function Add(...)
    local cnt = 0
    for i = 1,select("#",...) do
        cnt  = cnt + select(i,...)
    end
    return cnt
end
print(Add())
print(Add(1,2,3,4,5))
```

```bash
$ lua arg_select.lua 
3       4       5
7
0
15
```



#### thread类型

#### userdata

### 4、



