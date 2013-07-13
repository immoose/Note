# Chapter 2 Ruby.new
## ruby是一个面向对象的语言
+ constructor

```
song1 = Song.new("Ruby Tuesday")
```

## Some basic ruby
+ 函数

```
def say_goodnight(name)
  result = "Good night, " + name
  return result
end
# Time for bed
puts say_goodnight("Tim")
```

+ `#{表达式}` 会被替代为表达式的值

```
def say_goodnight(name)
  result = "Good night, #{name.capitalize}"
  return result
end
puts say_goodnight('uncle')    // produce Good night, Uncle
```

+ **双引号**作用
    + 可转义，例如`\n`
    + 可以执行表达式，例如`#{name.capitalize}`
    + 当表达式仅仅是一个全局实例或者是类变量，可以去掉大括号

```
$greeting = "Hello"
@name = "Produce"
puts "#$greeting, #@name"    // produce Hello, Produce
```

+ ruby函数的返回值就是最后一个表达式的值，所以可以不用`return`

```
def say_goodnight(name)
  "Good night, #{name}"
end
puts say_goodnight('Ma')
```

+ 命名规则
    + **本地变量、函数参数、函数名** 以小写字母或下划线开头
    + **全局变量** 以`$`开头
    + **实例变量** 以`@`开头
    + **类变量** 以`@@`开头
    + **类的名字、module的名字、常量** 以大写字母开头

## Arrays and Hashes
+ 两个都是indexed collection
+ array使用方括号[]建立

```
# array example
a = [1, 'cat', 3.14]
a[0]    # -> 1
a[2] = nil    # a -> [1, "cat", nil]
```

+ ruby中，nil是一个object，在条件判断中被视为false
+ `%w` creating arrays of words

```
a = %w{ant bee cat dog elk}  # a = ['ant' ,'bee', 'cat', 'dog', 'elk'];
```

+ hash使用大括号{}建立，=>左边是key，右边是value，key必须是唯一的

```
inst_section = {
      'cello'     => 'string',
      'clarinet'  => 'woodwind',
      'drum'      => 'percussion',
      'oboe'      => 'woodwind',
      'trumpet'   => 'brass',
      'violin'    => 'string'
}
inst_section['oboe'] → "woodwind" 
inst_section['cello'] → "string" 
inst_section['bassoon'] → nil
```

+ 当key不存在时，默认返回时nil。但是也可以更改这个默认返回

```
histogram = Hash.new(0)
#histogram['key1'] → 0
histogram['key1'] = histogram['key1'] + 1    #histogram['key1'] → 1
```

## Control Structures
+ if语句

```
if count > 10
    puts "Try again"
elsif tries == 3
    puts "You lose"
else
    puts "Enter a number"
end
```

+ while语句

```
while weight < 100 and num_pallets <= 30 
    pallet = next_pallet()
    weight += pallet.weight
    num_pallets += 1
end
```

+ 处理文件中的每一行

```
while line = gets         # gets 返回nil如果到达最后一行的话
    puts line.downcase
end
```

+ statement modifiers

```
puts "Danger, Will Robinson" if radiation > 3000
#means
if radiation > 3000
    puts "Danger, Will Robinson"

while square < 1000
    square = square * square
end
#means
square = square * square while square < 1000
```

## Regular Expressions
+ pattern的写法：`/pattern/`
+ 正则表达式也是一个object

```
/Perl|Python/    # 或
/P(erl|ython)/    # 同上
/ab+c/    # a 1个或者多个b c
/ab*c/    # a 0个或者多个b c
\s    # 空格字符
\d    # 数字
\w    # 字母
.    # 几乎所有字符 
```

+ 使用`=~`来匹配pattern，如果成功、则返回pattern开始的位置，否则返回nil

```
if line =~ /Perl|Python/
    puts "Scripting language mentioned: #{line}"
end
```

+ 使用正则替换

```
line.sub(/Perl/, 'ruby')    # replace first 'Perl' with 'Ruby'
line.gsub(/Python/, 'ruby')    # replace every 'Python' with 'Ruby'
line.gsub(/Perl|Python/, 'Ruby')
```

## Blocks and Iterations

```
{puts "Hello"}    # this is a block
do
    club.enroll(person)
    person.socilize
end    # and so is this
```

+ 声明

```
greet  { puts "Hi" }
verbose_greet("Dave", "loyal customer") { puts "Hi" }    # 带参数
```

+ 方法可以引入到block中，使用yield调用该方法

```
def call_block
    puts 'start of the block'
    yield
    yield
    puts 'end of the block'
end
call_block { puts 'in the block'}
# produces:
#    Start of method
#    In the block
#    In the block
#    End of method
```

+ 可以对`yield`提供参数，会被传至block中。

```
def call_block
    yield("hello", 99)
end
call_block {|str, num| ...}  # str = "hello", num = 99
```

+ 使用block实现iterator

```
animals = %w( ant bee cat dog elk)
animals.each {|animal| puts animal }
# produce
# ant
# bee
# cat
# dog
# elk
```

```
# with in the class Array
def each
    for each element     # not valid ruby, pseudo-code
        yield(element)
    end
end
```

例子

```
[ 'cat', 'dog', 'horse' ].each { |name| print name, " " }    # print不会换行
5.times { print "*" }
3.upto(6) { |i| print i }
( 'a' .. 'e' ).each { |char| print char }
# produces
# cat dog horse *****3456abcde
```

## Reading and Writing
+ `puts`和`print`都是输出到标准输出上
+ `printf`输出format的string
+ `gets`返回下一行到标准输入

```
line = gets
print line
```
