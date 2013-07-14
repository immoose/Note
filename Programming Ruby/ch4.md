# Chapter 4 Containers, Blocks and Iterators
## Containers
### Arrays
+ 初始化

```
a = [3.1415, "pie", 99];
a.class    -> Array
a.length    -> 3
b = Array.new
b[0] = 3.1415
b[1] = "pie"
```

+ []中如果是负数、则从尾部开始往前

```
a = [1, 3, 5, 7, 9]
a[-1]    -> 9
a[-2]    -> 7
a[-99]    -> nil
```

+ 子array：
    + `array[start, count]`
    + `array[start..end]` 两个点包含最后一项
    + `array[start...end]` 不包含最后一项

```
a = [1, 3, 5, 7, 9]
a[1, 3]    → [3, 5, 7]
a[3, 1]    → [7]
a[-3, 2]    → [5, 7]
a[1..3]    → [3, 5, 7] 
a[1...3]    → [3, 5] 
a[3..3]    → [7] 
a[-3..-1]    → [5, 7, 9]
```

+ `[]=` 能带两个参数，在这个位置上的array被等号右边的array替代；如果[]内长度为0，则在开始的位置之前插入等号右边的array。

```
a=[1,3,5,7,9] a[2, 2] = ’cat’
a[2, 0] = ’dog’ a[1,1]=[9,8,7] a[0..3] = []
a[5..6] = 99, 98
→ [1,3,5,7,9]
→ [1, 3, "cat", 9]
→ [1, 3, "dog", "cat", 9]
→ [1,9,8,7,"dog","cat",9]
→ ["dog", "cat", 9]
→ ["dog", "cat", 9, nil, nil, 99, 98]
```

+ `.push(object)` -> insert_object
+ `.shift` -> delete_first
+ `.pop` -> delete_last
+ def `[]` method
```
def [](index)
    @songs[index]
end
```

### Hashes

```
h = { 'dog' => 'canine', 'cat' => 'feline', 'donkey' => 'asinine' }
h.length → 3
h['dog'] → "canine"
h['cow'] = 'bovine'
h[12] = 'dodecine'
h['cat'] = 99
h → {"cow"=>"bovine", "cat"=>99, 12=>"dodecine",
                    "donkey"=>"asinine", "dog"=>"canine"}
```

## Blocks and Iterators
+ `find` iterator

```
def with_title(title)
    @song.find { |song| title == song.name }
end
```

> `i1, i2 = 1, 1  # i1 = 1 and i2 = 1`  
> `i1, i2 = i2, i1 + i2  # 斐波那契数列`

+ block variable scope

```
a = [1, 2]
b = 'cat'
a.each {|b| c = b * a[1] } 
a → [1,2] 
b→2 
defined?(c) → nil
```

+ `.collect` 把每个element传给block，返回一个新的collection

```
["H", "A", "L"].collect {|x| x.succ } → ["I", "B", "M"]
```

+ `.inject`  
如果带有括号，则第一个参数的初始值为括号的值
如果不带括号，则第一个element为第一个参数的初始值，从第二个参数开始遍历

```
[1,3,5,7].inject(0) {|sum, element| sum+element} → 16 
[1,3,5,7].inject(1) {|product, element| product*element} → 105

[1,3,5,7].inject {|sum, element| sum+element} → 16 
[1,3,5,7].inject {|product, element| product*element} → 105
```

+ `Kernel.block_given?`

```
class File
      def File.my_open(*args)
result = file = File.new(*args)
# If there's a block, pass in the file and close 
# the file when it returns
if block_given?
          result = yield file
          file.close
        end
        return result
      end
end
```

### Blocks Can Be Closures

+ 以`&`开头，即为code block。这个code block会被转换成Proc对象，并且赋给这个参数
+ 调用`Proc#call`

```
songlist = SongList.new
class JukeboxButton < Button
    def initialize(label, &action) 
        super(label)
        @action = action
    end
    def button_pressed
        @action.call(self)
    end 
end
start_button = JukeboxButton.new("Start") { songlist.start } 
pause_button = JukeboxButton.new("Pause") { songlist.pause }
```

```
def n_times(thing)
      return lambda {|n| thing * n }
end
p1 = n_times(23)
p1.call(3) → 69
p1.call(4) → 92
p2 = n_times("Hello ")
p2.call(3) → "Hello Hello Hello "
```

