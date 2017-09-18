---
layout: post
author: Thuong Nguyen
title: Sử dụng Sentry để theo dõi lỗi và sự kiện trong Javascript
description: Sử dụng Sentry để theo dõi lỗi và sự kiện trong Javascript
tags: ['tutorial', 'javascript', 'sip.js']
---

## Bài toán

Dạo gần đây mình được giao task làm module Call Center tích hợp vào trong CRM của công ty. Nói ngắn gọn thì công việc của mình là viết giao diện cho API của bên cung cấp dịch vụ Tổng Đài Gọi Điện thông qua giao thức SIP và tích hợp nó vào CRM.

Chuyện sẽ chẳng có gì cho đến khi sếp muốn sau khi gọi điện phải trả về kết quả là số điện thoại đó: có tồn tại, khách hàng từ chối nghe, số điện thoại đang tắt máy...
Nghe thì có vẻ dễ nhỉ? API nó trả về cái gì thì mình cũng chỉ việc trả về cái đó thôi chứ gì mà phải xoắn?
Thế nhưng lại khổ ở một cái là API nó không trả về rõ ràng như là ý sếp muốn tại vì gọi vào số điện thoại thật nó lại khác với SIP protocol.
Mà cái mình nhận về được nó là SIP response code.
Và có hơn [50 cái reponse codes](https://en.wikipedia.org/wiki/List_of_SIP_response_codes) cho một cuộc gọi <abbr title="Từ 4xx tới 6xx">fail</abbr> :'(.
Mình cũng có tìm được một số code đại diện để biết cuộc gọi nào là số không có thật, khách không bắt máy, khách từ chối trả lời hay là số máy đang bận.
Thế nhưng vẫn bị phản ánh từ bộ phận telesale là có những cuộc gọi kết thúc mà chả có thông báo gì cả.

Do đó sếp yêu cầu mình phải có một giải pháp để theo dõi tất cả cuộc gọi mỗi khi nó bị fail, thu thập response code, response header, số điện thoại, agent gọi, thời gian...
Nói chung là tất cả những thông tin có liên quan tới cuộc gọi và đưa ra báo cáo và cách giải quyết vấn đề.
Ở đây có thể bạn sẽ nghĩ là sao không build một cái backend rồi send những thông tin đó vào thôi?
Yup, đó cũng là một cách nhưng mà làm như thế thì không có vui, hehe. Cộng thêm là cần thời gian để build một backend, rồi giao diện để quản lý những thông tin gửi về.
Đối với mình lúc đó thì mình không có nhiều thời gian để build một backend.
Do đó mình quyết định tìm kiếm một giải pháp online và (tạm thời) miễn phí.

Sao một hồi tìm kiếm thì mình tìm được một giải pháp đó là [Sentry](https://sentry.io/)


## Thế Sentry là cái gì? Nó có cái gì hay hơn là tự tạo back-end để lưu thông tin?

### Giới thiệu về Sentry

Sentry là một Error Tracking & Crash Reporting software (tạm hiểu là phần mềm theo dõi và tự động báo lỗi cho developer) cho rất nhiều ứng dụng trên những tên tảng khác nhau, từ frontend cho tới backend website, từ desktop cho tới mobile, cho tới system log. Hỗ trợ tracking realtime và thông báo thông qua email

Đặt biệt, Sentry cho sử dụng free account. Free account có thể gửi được max 10.000 events/errors một tháng

### Sentry có gì đặc biệt?

Ừ thì gọi là đặc biệt cũng không hẳn, nhưng để tự xây dựng được những chức năng này thì cũng phải tốn khá nhiều thời gian và công sức đấy.

Thật sự thì lúc đầu mình cũng chả quan tâm là nó có tính năng gì hay ho, chỉ cần nó cho phép mình gửi thông tin theo ý muốn lên, và hỗ trợ mình xem lại những thông tin đó.
Thế nhưng sử dụng thì mới thấy nó có khá là nhiều tính năng thú vị.
(Ở đây mình chỉ nói về phần frontend website. Những ngôn ngữ về backend, desktop, phone... mình chưa dùng qua nên cũng chưa biết rõ nó có chức năng gì hay)

#### Sentry sẽ tự động gửi thông tin về hệ điều hành, trình duyệt, địa chỉtrang, IP của người dùng

Mỗi khi thông báo lỗi / sự kiện được gửi đi. Sentry sẽ tự động thu thập những thông tin liên qua như: Hệ điều hành, trình duyệt, địa chỉ IP, đường dẫn hiện tại, referrer... và những thông tin hữu ích khác để gửi kèm theo

![Figure 1](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/base-information.jpg "Figure 1")

#### Sentry cho phép bạn bổ sung thông tin liên quan trước khi gửi lỗi đi

Thông tin gửi thêm này được gọi là Additional Data. Bạn có thể gửi một JSON object và Sentry sẽ tự động làm format để bạn có thể dễ dàng theo dõi

![Figure 2](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/additional-data.jpg "Figure 2")

Đặc biệt một cái là Sentry sẽ tự động đoán xem cái nào là thông tin nhạy cảm và che lại. Những chỗ `[Filtered]` chính là những chỗ mà Sentry đã tự che lại.

#### Khi gửi thông tin đi Sentry gửi luôn cả thông tin trong console, bao gồm cả log, xhr request, action của người dùng lên UI

Đây là một trong những tính năng của Sentry mà mình thích nhất, nó được gọi là Breadcrumbs.
Breadcrumbs là những event của ứng dụng và trình duyệt được lưu lại.
Theo như trên trang tài liệu của Sentry thì mặc định Sentry sẽ tự động thu thập những thông tin sau:

- XMLHttpRequest
- Những thay đổi khi chuyển trang (Chỉ áp dụng cho HTML pushState)
- UI click và ấn phím
- Console logs
- Những lỗi trước (Khi chưa chuyển trang)

Ngoài ra bạn cũng có thể tạo ra những breadcrums cho riêng mình. Ví dụ như event khi khách hàng thêm sản phẩm vào giỏ hàng.
Chức năng này rất hữu ích, giúp bạn hiểu được người dùng đã thực hiện những thao tác nào dẫn tới lỗi xảy ra

![Figure 3](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/breadcrumbs.jpg "Figure 3")

#### Chức năng User Feedback để nhận phản hồi của người sử dụng mỗi khi có lỗi xảy ra

Chức năng này sẽ giúp các developer thu thập thông tin thêm từ người dùng bằng cách hiện thịra một popup hỏi các thông tin: Tên, email người dùng, và mô tả chuyện gì vừa xảy ra mỗi khi có lỗi xảy ra (Bạn có thể hoàn toàn control được khi nào thì cần phải hiện feedback popup)

![Figure 4](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/user-feedback.jpg "Figure 4")

#### Tạo user context, để biết được lỗi đó ảnh hưởng tới user nào

Bạn có thể tạo user context bằng cách sử dụng email hoặc ID của người dùng.
Sau khi tạo context xong thì những event trong Sentry có thể được lọc theo người dùng đã đăng nhập vào.

#### Liên kết với các dịch vụ issue tracker như: GitHub, Bitbucket, Trello... để tạo nhanh task

Sentry cho phép bạn kết nối với những ứng dụng như GitHub, Bitbucket, Trello và nhiều ứng dụng khác để tạo task nhanh khi có lỗi xảy ra.
Có mỗi một điều mình khó chịu ở cái Sentry này là nó bắt người dùng tự tìm API key, token điền vào.
Không phải như nhiều ứng dụng khác có một button, ấn và next next là xong

![Figure 5](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/trello-task.jpg "Figure 5")

Một ví dụ mình liên kết với Trello

#### Và nhiều tính năng khác nữa

Ngoài ra Sentry cũng còn những tính năng khác mà ở đây mình không kể ra hết đưọc.

- Filter bằng trình duyệt, IP, event, tags...
- Hỗ trợ source map trong việc gửi thông tin stack trace của exception
- ... còn những cái khác nữa để các bạn khám phá

## Cài đặt Sentry lên website

Okay, nói nãy giờ nhiều rồi. Bây giờ ta bắt tay vào việc làm thế nào để cài đặt được Sentry lên website

#### Tạo tài khoản Sentry

Trước tiên thì bạn cần phải có một tài khoản Sentry.
Bạn có thể đăng ký miễn phí ở [đây](https://sentry.io), trên cơ bản thì tài khoản có thể đăng ký miễn phí.
Thế nhưng bạn có thể tham khảo giá tại [đây](https://sentry.io/pricing/) nếu có hứng thú với các gói trả phí

#### Tạo một project mới

Sau khi tạo xong tài khoản Sentry và login vào bạn sẽ được hỏi và chọn ngôn ngữ/framework muốn tạo project.
Trên màn hình này bạn ấn vào tab Frontend và chọn framework bạn muốn sử dụng.
Sentry hỗ trợ khá nhiều framework: Vue.js, AngularJS, Angular 2+, React.js, Ember, Backbone.
Nếu bạn không sử dụng framework nào cả thì hoặc ngoài những cái trên thì bạn chọn Javascript.


#### Cài đặt Sentry vào ứng dụng của bạn

Sau khi tạo xong project mới thì tùy theo loại ngôn ngữ/framework mà bạn chọn, bạn sẽ được đưa tới trang hướng dẫn cài đặt cho ngôn ngữ/framework đó.

Ví dụ đối với mình là Vue project thì mình sẽ làm những bước sau

- Cài package `raven-js` vào ứng dụng của bạn

```bash
yarn add raven-js # Mình thích dùng yarn hơn là npm :D
```

- Import Raven vào ứng dụng Vue

```javascript
import Vue from 'vue';
import Raven from 'raven-js';
import RavenVue from 'raven-js/plugins/vue';

Raven
    .config('https://key@sentry.io/id') // key và id sẽ tự được điền ở trang hướng dẫn, bạn chỉ việc copy thôi.
    .addPlugin(RavenVue, Vue)
    .install();
```

- Okay, chỉ đơn giản như vầy thôi là bạn đã xong phần cài đặt Sentry. Bây giờ mỗi khi có exception thì nó sẽ tự động được gửi vào tài khoản Sentry của bạn.

#### Lập trình để tự gửi event theo ý muốn

Ngoài việc những error/exception được tự gửi đi thì bạn cũng có thể tự lập trình để gửi event đi.
Để làm việc đó thì bạn có thể sử dụng `Raven.captureMessage`.
Capture Message sẽ tự động gửi những thông tin giống như khi có exception xảy ra, ngoại trừ một điều là nó sẽ không có stack trace.

Ngoài việc sử dung `Raven.captureMessage` thì bạn có thể sử dụng `Raven.setExtraContext` để gửi thêm những thông tin cần thiết cho việc debug

```javascript
Raven.setExtraContext({response: {reponseSent: 'fromServer'})
Raven.captureMessage(`Failed event fired with status code: ${statusCode}`)
```

Okay, vậy là bạn đã hoàn thành hệ thống error tracking and crash reporting cho trang web của bạn rồi đó :D.
Bạn có thể xem thêm thông tin tài liệu ở [đây](https://docs.sentry.io/)

---

Bài hôm nay kết thúc tại đây. Hẹn gặp lại các bạn vào bài những bài tiếp theo.










