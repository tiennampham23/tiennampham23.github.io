---
layout: post
title:  "Tìm hiểu về Call stack Javascript"
date:   2020-03-19 21:25:36 +0530
categories: Javascript Backend Frontend Callstack 
---
Chào các bạn, hôm nay mình sẽ cùng các bạn giới thiệu về Callstack, Event Loop, Heap trong Javascript.

# Callstack là gì?
Trước hết, một trong những điều chúng ta cần lưu ý đó là: `Javascript(JS) là ngôn ngữ chỉ có a single thread`, điều đó có nghĩa là tại một thời điểm nhất định, JS chỉ có thể xử lý một công việc mà thôi(lul). Javascript bao gồm single call stack, heap, queue. Chúng ta sẽ cùng nhau tìm hiểu từng thành phần nhé.

![Ảnh: Medium](https://miro.medium.com/max/299/1*ZSFHnq9iMHIApVLcgwczPQ.png)

## CallStack

Q: Callstack là gì?

A: Callstack là một data structure để có thể chứa các hàm gọi, nếu bạn gọi một hàm để thực hiện, bạn sẽ đẩy `một cái gì đó` vào trong stack, khi bạn trả lại kết quả của hàm, bạn cũng sẽ lấy `một cái gì đó` ra khỏi stack.
![Callstack](https://miro.medium.com/max/1200/1*E3zTWtEOiDWw7d0n7Vp-mA.gif)

```javascript
function foo(b) {
    var a = 5;
    return a * b + 10;
}

function bar(x) {
    var y = 3;
    return foo(x * y);
}

console.log(bar(5))
```
- Đầu tiên, đoạn code trên sẽ bắt đầu từ hàm `main()`, chính là hàm wrap đoạn code trên lại và thực thi.
- Tiếp theo, đoạn code chạy đến `console.log(bar(5))`, `console.log(bar(5))` lúc này sẽ được đẩy vào trong stack.
- Khi đó `bar(5)` lại được gọi, và tiếp tục được đẩy vào đỉnh của stack
- Tương tự đối với `foo()` cũng được đẩy trực tiếp lên đỉnh của stack.
- Hàm `foo()` ngay lập tức `trả lại` kết quả, như vậy, `foo()` sẽ được lấy ra khỏi stack ngay sau khi `return`
- Hàm `bar()` cũng được return lại kết quả và được đẩy ra khỏi stack
- Cuối cùng, dòng code `console.log()` được lấy ra khỏi stack và in ra kết quả.


Như vậy chúng ta có thể lấy, khi thực hiện 1 block code, chúng ta đẩy từng `function` vào trong stack, và lấy ra khi hàm đó `return` lại kết quả. Javascript chạy lần lượt code từ trên xuống dưới, một cách rất `sync` phải không nào?


<b>Lưu ý:</b> Thỉnh thoảng, chúng ta sẽ gặp phải lỗi lặp vô hạn khi chúng ta gọi 1 hàm đệ quy nào đó mà không có điểm dừng, như vậy là chúng ta sẽ đẩy vào trong stack các frames liên tục, dẫn đến hiện tượng `stack overflow`(nghe quen chưa :D ).
![](https://i.imgur.com/kOdmvKt.png)

Và brower sẽ bắn ra lỗi sau:

![](https://miro.medium.com/max/837/1*tqkykdU69DFrxi82JOWLbQ.png)


