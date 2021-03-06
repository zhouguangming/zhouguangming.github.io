---
layout: post
---

我们知道在 Ruby 的方法中有两种方式可以执行传入的代码块:

第一种是使用 yield 关键字:

```ruby
def say
  yield
end

say { 'Hello, World!' }

=> 'Hello, World!'
```

第二种是使用 & 符号定义参数:

```ruby
def say &block
  block.call
end

say { 'Hello, World!' }

=> 'Hello, World!'
```

由于第二种方式在调用时每次都会生成一个 Proc 对象, 在性能上要比第一种方式慢数倍, 所以尽量不要使用, 除非你确定需要一个 Proc 对象.

下面是 Benchmark:

```ruby
require 'benchmark'

def with_block(&block); block.call end
def with_yield;         yield      end

Benchmark.bm do |x|
  x.report('with &block') do
    1_000_000.times { with_block { 'hello, world' } }
  end

  x.report('with  yield')  do
    1_000_000.times { with_yield { 'hello, world' } }
  end
end

# outputs

       user     system      total        real
with &block  1.170000   0.010000   1.180000 (  1.185767)
with  yield  0.220000   0.000000   0.220000 (  0.223367)
```

另有一个好玩的 trick: 在方法内使用 Proc.new 若后面没有跟一个代码块, 这个 Proc 对象将会默认使用方法外部的代码块.

```ruby
def say
  Proc.new.call
end

say { 'Hello, World!' }

=> 'Hello, World!'
```
