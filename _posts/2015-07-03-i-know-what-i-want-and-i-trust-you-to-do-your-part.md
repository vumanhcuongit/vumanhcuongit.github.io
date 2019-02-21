---
layout: post
title: "Tôi biết cái tôi muốn và tôi tin chắc bạn sẽ làm được"
description: ""
categories:
  - Ruby
tags:
 - code review
 - ruby
excerpt: "Hôm nay mình muốn giới thiệu bí quyết trên của Sandi Metz, một tác giả yêu thích của mình về các vấn đề liên quan tới OOP. Mình thật sự ấn tượng với nó vì chỉ một câu ngắn gọn thôi cũng đủ để xem là kim chỉ nam trong việc thiết kế các class."
---

> “I know what I want and I trust you to do your part.” - Sandi Metz

Hôm nay mình muốn giới thiệu bí quyết trên của [Sandi Metz](http://www.sandimetz.com/), một tác giả yêu thích của mình về các vấn đề liên quan tới OOP. Mình thật sự ấn tượng  với nó vì chỉ một câu ngắn gọn thôi cũng đủ để xem là kim chỉ nam trong việc thiết kế các *class*.


## Sự giao tiếp giữa các đối tượng với nhau
> "Object-oriented applications are made up of objects and the messages that pass between them" - Sandi Metz

Trong lập trình hướng đối tượng, mỗi class được dùng để mô hình hoá một thực thể trong thế giới thực. Mỗi đối tượng thuộc về một class sẽ có các thuộc tính và cách ứng xử (behaviour) giống nhau, các cách ứng xử này được định nghĩa bởi các method. Các đối tượng giao tiếp với nhau bằng việc gửi đi những thông điệp(message): một đối tượng sẽ thực hiện những công việc khi nhận được một thông điệp thích hợp từ một đối tượng khác ~ gọi hàm của một đối tượng khác.

Để giao tiếp tốt với nhau, một đối tượng phải hiểu biết đối tượng kia (tên hàm, business logic,...), điều này dẫn đến việc lệ thuộc lẫn nhau. Càng hiểu biết nhiều thì sự lệ thuộc càng nhiều, việc này sẽ gây khó khăn cho việc sử dụng lại (reuse) cũng như mở rộng ứng dụng.

## Ba kiểu thiết kế class
Để giới thiệu ba kiểu thiết kế class cũng như muốn chỉ ra kiểu thiết kế nào là tốt nhất, mình sử dụng case study như sau để minh hoạ:

*Trên website bán hàng của cửa hàng Mido, sau khi khách hàng đã thêm một số sản phẩm vào giỏ hàng, thì khi khách hàng nhấn nút "Thanh toán" ở trang thông tin giỏ hàng, website sẽ tạo một đơn đặt hàng và thực hiện thêm hai công việc như sau:*

 * *Kiểm tra tính hợp lệ của sản phẩm trong giỏ hàng: sản phẩm còn hay hết, ...*
 * *Áp dụng giá ưu đãi cho sản phẩm nếu đang trong giai đoạn khuyến mãi.*


### 1. Tôi biết cái tôi muốn và tôi biết bạn làm điều đó như thế nào
![type1](http://s12.postimg.org/qafbkpai5/example1.png)

{% highlight ruby %}
class Cart
  def checkout(order)
    items.each do |item|
      order.check_item(item)
      order.adjust_amount(item)
    end
  end
end
{% endhighlight %}

Ở cách thiết kế class như thế này, *Cart* muốn thực hiện thanh toán và nó biết khá nhiều về việc mà *Order* làm. *Cart* biết khi thanh toán giỏ hàng thì *Order* sẽ kiểm tra giỏ hàng và cập nhật giá khuyến mãi. Với việc này thì code của *Cart* sẽ thay đổi bất cứ lúc nào khi  *Order* thêm hoặc xoá bớt một số bước trong việc thanh toán. Ta có thể nói *Cart* lệ thuộc quá nhiều vào *Order*.

### 2. Tôi biết cái tôi muốn và tôi biết bạn làm cái gì
![type2](http://s22.postimg.org/b3y5lmsqp/example1_1.png)

{% highlight ruby %}
class Cart
  def checkout(order)
   items.each do |item|
     order.update_order(item)
   end
  end
end

class Order
  def update_order(item)
   check_item(item)
   adjust_amount(item)
  end
  #...
end
{% endhighlight %}

Cách thiết kế thứ hai này là một sự cải tiến của cách thứ nhất, *Cart* bây giờ không còn biết rõ cách *Order*  làm việc, nó chỉ biết là *Order* sẽ cập nhật hoá đơn với những sản phẩm (items) trong giỏ hàng khi thực hiện việc thanh toán. Với việc ít lệ thuộc vào *Order* hơn thì *Cart* đã hạn chế được việc bị thay đổi code.
Nhưng *Cart* vẫn còn biết một chút về việc *Order* thực hiện thanh toán ~ *Cart* vẫn còn lệ thuộc chút ít vào *Order*. Chúng ta vẫn có thể thiết kế class một cách tốt hơn nữa!

### 3. Tôi biết cái tôi muốn và tôi tin chắc bạn sẽ làm được
![type3](http://s17.postimg.org/hb1txluq7/example1_2.png)
{% highlight ruby %}
class Cart
  def checkout(order)
    order.checkout(self)
  end
end

class Order
  def checkout(cart)
   cart.items.each do |item|
     check_item(item)
     adjust_amount(item)
   end
  end
  #...
end
{% endhighlight %}

Với cách thiết kế thứ ba này, *Cart* không biết một thứ gì về cách *Order* thực hiện công việc thanh toán. *Cart* chỉ nói cho *Order* biết rằng nó muốn thanh toán và chỉ gửi chính đối tượng của nó tới *Order* và tin rằng *Order* sẽ hoàn tất việc thanh toán.
Tất cả các tác vụ trong việc thanh toán giờ đã được chuyển hẳn qua cho *Order*, điều này giúp cho cả hai class dễ tái sử dụng, dễ mở rộng và dễ test.

## Những cách để áp dụng tốt bí quyết này

### Thay đổi mindset trong việc thiết kế class
Mình không biết các bạn được hướng dẫn thiết kế các class như thế nào trong trường đại học, nhưng với riêng mình thì lúc đi học mình được hướng dẫn là hãy đọc mô tả và lấy bút khoanh tròn những danh từ, đa số các danh từ đó sẽ là các class. Hệ quả của việc thiết kế class theo cách này là với những class có sẵn bạn sẽ nhồi nhét những function vào cho chúng, có thể dẫn đến các kết quả xấu như "fat controller, fat model", vi phạm quy tắc [single responsibility](https://en.wikipedia.org/wiki/Single_responsibility_principle).

Vì vậy bạn cần phải thay đổi suy nghĩ từ *"mình có những đối tượng như thế này, chúng sẽ làm những chức năng gì?"* sang *"mình muốn gửi thông điệp (message) này đi thì đối tượng nào sẽ có trách nhiệm trả lời nó?"*. Bạn không gửi đi các thông điệp bởi vì những đối tượng có sẵn, nhưng bạn có những đối tượng từ việc gửi đi các thông điệp.

### Hãy sử dụng sequence diagram
Hãy sử dụng [sequence diagram](https://en.wikipedia.org/wiki/Sequence_diagram) trong việc thiết kế class, nhìn vào đó bạn sẽ có cái nhìn bao quát về các luồng đi của các thông điệp.
Bạn có thể vẽ trên giấy hay trên máy tính đều được miễn sao cho hiệu quả và thoải mái. Các hình vẽ trong log này dùng [Gliffy](https://www.gliffy.com) trên giao diện website để vẽ.

## Fun facts
{% highlight ruby %}
wife.husband.get_key.open_box.open_wallet.get_money # vợ biết cái vợ muốn và vợ biết chồng làm điều đó như thế nào

wife.husband.get_money # vợ biết cái vợ muốn và vợ biết chồng làm cái gì

Husband.be_asked_for_money(wife) # vợ biết cái vợ muốn và vợ tin chắc chồng sẽ làm được
{% endhighlight %}

## Tham khảo
- Các bạn sẽ không hối tiếc khi đọc quyển [Practical Object-Oriented Design in Ruby](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330/ref=sr_1_1?ie=UTF8&qid=1433259631&sr=8-1&keywords=practical+object+oriented) của Sandi Metz.
- Một số ý trong log này được lấy ra từ trong quyển sách đã giới thiệu ở bên trên.

Happy coding!

*tựa đề dễ gây lầm tưởng đây là dạng bài hạt giống cho tâm hồn :3*