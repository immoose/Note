# Chapter 1 Getting Started
## Running Ruby
### Interacitve ruby
+ 使用`ruby`指令
```
% ruby
puts "Hello, world!"
^D
Hello, world!
```

+ irb(Interactive ruby)  
irb is a ruby shell
```
% irb
irb(main):001:0> def sum(n1, n2)
irb(main):002:1> n1 + n2 
irb(main):003:1> end
=> nil
irb(main):004:0> sum(3, 4)
=> 7
irb(main):005:0> sum("cat", "dog") 
=> "catdog"
```

### Ruby Programs
+ run from file
```
% ruby myprog.rb
```

+ use Unix "shebang"
```
#!/usr/local/bin/ruby -w
puts "hello world!"
```

+ 可执行文件形式
```
% chmod +x myprog.rb
% ./myprog.rb
```

## Ruby Documentation: RDoc and ri
+ 如果源代码使用RDoc进行文档注释，它的文档可以被转换成HTML和ri格式。
+ 通过ri查找文档

```
% ri GC           // list the summary of GC class
% ri enable       // find information on a particular method
% ri start
% ri GC.start
% ri --help
```
