---
layout: post
title: "Ứng dụng code review trong quá trình phát triển dự án"
description: ""
categories:
  - Ruby
tags:
 - code review
 - ruby
excerpt: "Áp dụng code review trong quá trình phát triển dự án sẽ mang lại lợi ích to lớn cho nhóm lập trình cũng như cho từng thành viên trong nhóm."
---

Áp dụng code review trong quá trình phát triển dự án sẽ mang lại lợi ích to lớn cho nhóm lập trình cũng như cho từng thành viên trong nhóm. Bài viết này sẽ giới thiệu về:

* Code review là gì ?

* Lợi ích cũng như khó khăn khi áp dụng code review.

* Những cách làm cho việc áp dụng code review thêm hiệu quả.

## Code review là gì ?
Code review là quá trình mà các lập trình viên xem xét và đánh giá code của một thành viên khác trong nhóm. Đó cũng là quá trình thảo luận với nhau trên các dòng code, qua đó đưa ra những góp ý hữu ích làm cho các dòng code thêm chất lượng hơn.

![review code example](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/github-code-review-example2.png_lvleo69hvs)

*ảnh minh họa. Nguồn: kumar303*


## Lợi ích của code review
–  Ngoài việc viết test, code review cũng là một cách để giảm thiểu số lượng lỗi phát sinh. Đôi khi vì quá quen với những dòng code của chính mình, chúng ta khó phát hiện lỗi hơn là người khác.

–  Thông qua code review, các thành viên trong nhóm sẽ nắm bắt được những gì đang diễn ra trong dự án, biết rõ ai đang phụ trách phần nào và người đó thực hiện việc như thế nào.

–  Được học hỏi thêm nhiều kiến thức hữu ích. Việc được học hỏi đúng nơi (code của mình), đúng thời điểm (đang viết code) giúp chúng ta nhớ lâu hơn.

–  Học cách giảng giải và góp ý cho người khác. Đây cũng là một cơ hội để chúng ta ôn lại & kiểm nghiệm lại kiến thức của mình.

> Nếu bạn không thể giải thích đơn giản một vấn đề thì bạn chưa hiểu đủ rõ về nó – **Einstein**

–  Thông qua việc học hỏi và trao đổi lẫn nhau, chúng ta có thể viết ra được những dòng code chất lượng. Hơn thế nữa, chúng ta có thể áp dụng những cái học được qua các dự án tiếp theo.

## Khó khăn khi áp dụng code review
Chúng ta sẽ tốn thời gian nhiều hơn bình thường vì phải thực hiện: xem xét code, sữa chữa và cập nhật code.

Theo ý kiến cá nhân của mình thì đây là một sự đánh đổi chấp nhận được, để có những dòng code chất lượng hơn. Vì ngược lại, những dòng code không tốt có thể phát sinh ra lỗi và bạn sẽ tốn nhiều thời gian hơn để sữa những lỗi đó.

## Những cách giúp việc thực hiện code review thêm hiệu quả

### Hãy tử tế, vì một lập trình viên tử tế sẽ:

  * sẵn lòng chia sẻ kiến thức của mình với người khác.

  * đưa ra những góp ý mang tính xây dựng.

  * không lợi dụng code review vào mục đích công kích người khác.

  * luôn muốn tiếp thu những góp ý hữu ích của người khác để hoàn thiện mình hơn.

  * biết trân trọng những đóng góp của các thành viên khách trong nhóm.

Một lập trình viên tử tế sẽ không làm cho người viết code bị tự ái, nội bộ team bị chia rẽ. Dưới đây là góp ý của mình về việc comment code dựa theo các ví dụ:

>**bad:**
"Tui đọc trên mạng thấy vậy / Google đi chú, cái này dễ mà" ==> *comment quá mông lung, không có tính xây dựng.*
>
>**good:**
 "Mình tham khảo dựa trên [bài viết](http:://google.com) này, mình nghĩ có thể áp dụng được vào trường hợp trên, @nguyenvana  nghĩ như thế nào ?"

>**bad:**
"WTH / WTF are you doing? / Sai rồi !"  ==> *comment rất thiếu tính xây dựng, không tôn trọng người viết code, làm người khác tự ái, nội bộ team có thể bị chia rẽ.*
>
>**good:**
 "Mình thấy phần này bạn làm chưa đúng. Theo như yêu cầu của ticket [#1081](http:example.com) thì output phải là ... Bạn kiểm tra lại thử xem ?"

> **bad:**
"Ờ thì tuỳ bạn thôi, thích thì cứ làm vậy đi." ==> *thái độ buông xuôi, không quyết tâm tranh luận để tìm ra điều đúng.*
>
>**good:**
"Có lẽ mình diễn giải chưa rõ, ý mình là ... @nguyenvana nghĩ như thế nào ?"


### Hãy mô tả một issue thật rõ ràng

Vì khi đó chúng ta sẽ biết phải làm gì. Thông qua việc mô tả issue rõ ràng, người review code sẽ hiểu rõ hơn về việc chúng ta đang làm, giúp cho việc review code thêm thuận lợi và ít tốn thời gian hơn.

![review code process](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/create-issue.png_faxexbrgem)

Để có một phần mô tả issue thật rõ ràng, mình góp ý các bạn nên trả lời 4 câu hỏi sau vào phần mô tả của issue.

1. Mục đích của việc tạo issue là gì ?
2. Yêu cầu và mong muốn đạt được của issue là gì ?
3. Cách để xử lý issue như thế nào ?
4. Cần những tham khảo nào ?


Sau đây là một mô tả ví dụ:

![example issue](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/example-issue.png_iwhbo3ibck)

Hy vọng các bạn sẽ mạnh dạn áp dụng code review vào những dự án của mình. Mong nhận được những chia sẻ cũng như góp ý của các bạn về bài viết này.
Cám ơn các bạn.
