# Chapter 3 Class, Objects, and Variables
## Introduction
+ `initialize`方法

```
class Song
    def initialize(name, artist, duration)
        @name = name
        @artist = artist
        @duration = duration
    end
end
```

+ 创建对象以及`inspect`方法

```
song = Song.new("Bicylops", "Fleck", 269)
song.inspect    # 任何对象可以调用inspect，输出格式化的object id和实例变量
# produce : <Song:0x1c7ca8 @name="Bicylops", @duration=260, @artist="Fleck">
```

+ `to_s`方法
```
song.to_s    # to_s : to_string
# produce : <Song:0x1c7ca8>
```

一般需要override `to_s`，ruby的方法从不关闭，可以随意给任意class添加方法
```
class Song
    def to_s
        "Song: #@name--#@artist (#@duration)"
    end
end
song = Song.new("Bicylops", "Fleck", 260)
song.to_s
# produce "Song: Bicylops--Fleck (260)"
```

## Inheritance and Messages

```
class KaraokeSong < Song
    def initialize(name, artist, duration, lyrics)
        super(name, artist, duration)
        @lyrics = lyrics
    end
    def to_s
        super + " [#@lyrics]"
    end
end
```

## Objects and Attributes

### Read Attributes
+ ruby只能有一个父类（单继承），但是能包含任何数量的mixin（mixin就像一个类定义的部分）
+ `attr_reader` reader accessor methods

```
class Song
    attr_reader :name, :artist, :duration
end
# equals to
class Song
    def name
        @name 
    end
    def artist
        @artist
    end
    def duration
        @duration
    end
end

song = Song.new("Bicylops", "Fleck", 260) 
song.artist → "Fleck"
song.name → "Bicylops" 
song.duration → 260
```

### Writable Attributes
+ `attr_writer` writer accessor method

```
class Song
    def duration=(new_duration)
        @duration = new_duration
    end
end
# equals to
class Song
    attr_writer :duration
end

song = Song.new("Bicylops", "Fleck", 260) 
song.duration = 257
```

### Virtual Attributes
+ 使用attribute method新建一个虚拟的attribute

```
class Song
    def duration_in_minutes
@duration/60.0 # force floating point end
    def duration_in_minutes=(new_duration)
        @duration = (new_duration*60).to_i
    end
end
song = Song.new("Bicylops", "Fleck", 260) 
song.duration_in_minutes → 4.33333333333333
song.duration_in_minutes = 4.2
song.duration → 252
```

### Attributes, Instance Variables, and Methods
+ internal state -> instance variables
+ external state -> attributes
+ methods

## Class Variables and Class Methods
### Class Variables
+ 以`@@`开头

```
class Song
    @@plays = 0    # class variable
    def initialize(name, artist, duration)
        @name     = name
        @artist   = artist
        @duration = duration
        @plays    = 0    # instance variable, not class variable 
    end
end
```

### Class Methods

```
class Example
  def instance_method
  end
  def Example.class_method
  end
end
```

```
class SongList
    MAX_TIME = 5*60 # 5 minutes
    def SongList.is_too_long(song)
    return song.duration > MAX_TIME
    end 
end
song1 = Song.new("Bicylops", "Fleck", 260) 
SongList.is_too_long(song1) → false
```

+ 其余的定义class methods的方法

```
class Demo
    def Demo.meth1
        # ... 
    end
    def self.meth2
        # ...
    end
    class <<self
        def meth3
            # ... 
        end
    end 
end
```

### Singletons and Other Constructors

```
class MyLogger
    private_class_method :new
    @@logger = nil
    def MyLogger.create
        @@logger = new unless @@logger
        @@logger
    end
end
```

## Access Control
+ ruby的access control 是运行期获取的
+ `private`：类和子类都可以在类内部调用，不可以指定调用者，默认为self
+ `protected`：类和子类都能在类内部调用，可以指定调用者。能够被任意该类或者是它的子类的实例调用。
```
class MyClass
    def method1    # default is public
        #...
    end
    protected
    def method2
        #...
    end
    private
    def method3
        #...
    end 
    public
    def method4
        #...
    end 
end
```

```
class MyClass
    def method1
    end
    # ... other methods
    public    : method1, method4
    private   : method2
    protected : method3
end
```

## Variables
+ **指针传递**变量对象
+ aliasing问题

```   
person1 = "Tim"
person2 = person1
person1[0] = 'J' 
person1 → "Jim"
person2 → "Jim"
```

+ 避免aliasing的方法：
    + .dup
    + .freeze

```
person1 = "Tim" 
person2 = person1.dup 
person1[0] = "J" 
person1 → "Jim" 
person2 → "Tim"
```

```
person1 = "Tim"
person2 = person1
person1.freeze # prevent modifications to the object 
person2[0] = "J"
# produce
# prog.rb:4:in `[]=': can't modify frozen string (TypeError) from prog.rb:4
```
