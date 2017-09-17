---
layout: post
author: Thuong Nguyen
title: Sử dụng Sentry để track lỗi và những sự kiện trong Javascript
tags: ['tutorial', 'javascript', 'sip.js']
---

## Bài toán

Dạo gần đây mình được giao một task làm module Call Center tích hợp vào trong CRM của công ty. Nói ngắn gọn thì công việc của mình là viết giao diện cho API của bên cung cấp dịch vụ Tổng Đài Gọi Điện thông qua giao thức SIP và tích hợp nó vào CRM.

Chuyện sẽ chẳng có gì cho đến khi sếp muốn sau khi gọi điện phải trả về kết quả là số điện thoại đó: có tồn tại, khách hàng từ chối nghe, số điện thoại đang tắt máy...
Nghe thì có vẻ dễ nhỉ? API nó trả về cái gì thì mình cũng chỉ việc trả về cái đó thôi chứ gì mà phải xoắn?
Thế nhưng lại khổ ở một cái là API nó không trả về rõ ràng như là ý sếp muốn tại vì gọi vào số điện thoại thật nó thiết kế khác với lại SIP protocal.
Mà cái mình nhận về được nó là SIP response code.
Và có hơn [50 cái reponse codes](https://en.wikipedia.org/wiki/List_of_SIP_response_codes) cho một cuộc gọi <abbr title="Từ 4xx tới 6xx">fail</abbr> :'(.
Mình cũng có tìm được một số code đại diện để biết cuộc gọi nào là số không có thật, khách không bắt máy, khách từ chối trả lời hay là số máy đang bận.
Thế nhưng vẫn bị phản ánh từ bọ phận telesale là có những cuộc gọi kết thúc mà chả có thông báo gì cả.

Do đó sếp yêu cầu mình phải có một giải pháp để theo dõi tất cả cuộc gọi mỗi khi nó bị fail, thu thập response code, response header, số điện thoại, agent gọi, thời gian...
Nói chung là tất cả những thông tin có liên quan tới cuộc gọi và đưa ra báo cáo và cách giải quyết vấn đề.
Ở đây có thể bạn sẽ nghĩ là sao không build một cái back-end rồi send những thông tin đó vào thôi?
Yup, đó cũng là một cách nhưng mà làm như thế thì không có vui, hehe. Cộng thêm là cần thời gian để build một back-end, rồi giao diện để quản lý những thông tin gửi về.
Đối với mình lúc đó thì mình không có nhiều thời gian để build một back-end.
Do đó mình quyết định tìm kiếm một giải pháp online và (tạm thời) miễn phí.

Sao một hồi tìm kiếm thì mình tìm được một giải pháp đó là [Sentry](https://sentry.io/)


## Thế Sentry là cái gì? Nó có cái gì hay hơn là tự tạo back-end để lưu thông tin?

### Giới thiệu về Sentry

Sentry là một Error Tracking & Crash Reporting software (tạm hiểu là phần mềm theo dõi và tự động báo lỗi cho developer) cho rất nhiều ứng dụng trên những tên tảng khác nhau, từ front-end cho tới back-end website, từ desktop cho tới mobile cho tới system log. Hỗ trợ tracking realtime và thông báo thông qua email

Đặt biệt, Sentry cho sử dụng free account. Free account có thể gửi được max 10.000 events/errors một tháng

### Sentry có gì đặc biệt?

Ừ thì gọi là đặc biệt cũng không hẳn, nhưng để tự xây dựng được những chức năng này thì cũng phải tốn kha khá thời gian đấy.

Thật sự thì lúc đầu mình cũng chả quan tâm là nó có tính năng gì hay ho, chỉ cần nó cho phép mình gửi thông tin theo ý muốn lên, và hỗ trợ mình xem lại những thông tin đó.
Thế nhưng sử dụng thì mới thấy nó có khá là nhiều tính năng thú vị.
(Ở đây mình chỉ nói về phần front-end website. Những ngôn ngữ back-end, desktop, phone... mình chưa dùng qua nên cũng chưa biết rõ nó có chức năng gì hay)
