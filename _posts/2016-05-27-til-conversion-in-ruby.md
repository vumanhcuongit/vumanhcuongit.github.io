---
layout: post
title: "[TIL] #1 Chuyển đổi kiểu dữ liệu tường minh và không tường minh trong Ruby"
description: ""
categories:
  - Ruby
tags:
 - til
 - ruby
excerpt: "Sự khác nhau giữa explicit và implicit conversion trong Ruby."
---

 *chú thích thuật ngữ*

 - explicit: tường minh
 - implicit: không tường minh
 - conversion: chuyển đổi kiểu dữ liệu
 - target class: kiểu class mà mình muốn chuyển đổi về
 - runtime: thời điểm thực thi dòng lệnh
 - string interpolation: chuỗi nội suy

### Một số hàm chuyển đổi trong Ruby

![conversion table](http://s33.postimg.org/7n6c02wsf/Screen_Shot_2016_05_27_at_9_09_19_PM.png)

### Hàm chuyển đổi tường minh
 - Chuyển đổi một đối tượng có kiểu dữ liệu hoàn toàn khác với target class.
 - Ruby sẽ **không tự động** gọi các hàm này trong lúc thực thi dòng lệnh. Chỉ có một ngoại lệ với *chuỗi nội suy*.

Ví dụ:

{% highlight ruby %}
"123".to_i # bạn nói với Ruby rằng hãy chạy lệnh #to_i

"The time is now: #{Time.now}" # trường hợp ngoại lệ
{% endhighlight %}

### Hàm chuyển đổi không tường minh
 - Chuyển đổi một đối tượng có kiểu dữ liệu tương đồng với target class.
 - Ruby sẽ **tự động** gọi các hàm này (ví dụ #to_str, #to_ary)  để đảm bảo rằng đối tượng cần chuyển đổi có cùng kiểu với target class.

Ví dụ:

{% highlight ruby %}
class Foo
 def to_str
  "Foo"
 end
end

class Bar; end

"Hello " + Foo.new
=> "Hello Foo"

"Hello " + Bar.new
=> TypeError: no implicit conversion of Bar into String
{% endhighlight %}

### Khi nào nên dùng?

- Nếu không cần quan tâm kiểu dữ liệu trước đó của parameter là gì thì dùng kiểu chuyển đổi tường minh.

Ví dụ:

{% highlight ruby %}
def display_number(number)
 number = number.to_i
 puts "It is #{number}"
end

display_number(1)
=> It is 1

display_number("2")
=> It is 2
{% endhighlight %}

- Nếu muốn kiểm soát chặt chẽ kiểu dữ liệu trước đó của parameter thì dùng kiểu chuyển đổi không tường minh.

Ví dụ:

{% highlight ruby %}
def display_number(number)
 number = number.to_int
 puts "It is #{number}"
end

display_number(1)
=> It is 1

display_number("2")
=> NoMethodError: undefined method 'to_int' for "2":String
{% endhighlight %}

Happy coding!

p/s: bài đầu tiên mở màn cho topic Today I Learned [TIL]
