---
layout: post
title:  "Tạo multipart/form-data với framework Flask"
date:   2020-03-17 21:03:36 +0530
categories: Python Flask Rest Web Backend
---

Flask is a lightweight WSGI web application framework. It is designed to make getting started quick and easy, with the ability to scale up to complex applications. It began as a simple wrapper around Werkzeug and Jinja and has become one of the most popular Python web application frameworks.

Flask offers suggestions, but doesn't enforce any dependencies or project layout. It is up to the developer to choose the tools and libraries they want to use. There are many extensions provided by the community that make adding new functionality easy.



Đoạn trên mình copy trang chủ thôi, bạn thích thì đọc, không thích thì đọc.
# Flask là gì? Tại sao nên sử dụng Flask?
```The Python micro framework for building web applications```

Flask là một `micro framework` được sử dụng để xây dựng ứng dụng web. Flask là một [lightweight WSGI][wsgi] framework, nó được thiết kế để tạo các ứng dụng web <b>nhanh</b> và <b>dễ dàng</b> hơn.
Vì thế, hiện nay framework này được các nhà phát triển phần mềm sử dụng rộng rãi để phát triển ứng dụng web của mình.

[Mirco framework][microframework] là một thuật ngữ được sử dụng để miêu tả các khuân khổ ứng dụng web tối giản. Tức là bản chất `micro framework` thiếu hầu hết các chức năng thường có của một framework như là:
- Authentication, authorization, roles...
- Database abstraction(ORM - object ralational mapping)
- Web template engine(Flask vẫn sử dụng Jinja2).

Thông thường, một `micro framework` nhận về một HTTP request, routing HttpRequest đến controller thích hợp, xử lý logic trong controller, trả lại kết quả là một HTTP Request.
Microframework rất phù hợp cho việc xây dựng API cho các services hoặc applications.
# Xin chào, mình là Nam
Đối với mỗi lập trình viên khi bắt đầu một ngôn ngữ hoặc framework mới, đầu tiên là chúng ta tạo project Helloworld đúng không nào =)).

Ok. Giờ mình sẽ tạo 1 project là Hello `Nam` nhé, Nam là cả thế giới, của ai đó Lol =)))

### Đầu tiên, chúng ta cùng cài đặt Virtualenv lên nào:
```python
    # install pip first
    sudo apt-get install python3-pip
    # Then install virtualenv using pip3
    sudo pip3 install virtualenv 
    # Now create a virtual environment
    virtualenv venv
    # Active your virtual environment:
    source venv/bin/activate
```

### Như vậy, mình đã cài đặt thành công virtualenv và active nó lên, tiếp theo, bạn cần tạo một file app.py để có thể code.

```shell
    touch app.py
```
### Ở đây, để sự dụng Flask, mình cần cài đặt Flask bằng pip
```shell
    pip install Flask
```

### Tiếp theo, mở file app.py lên và bắt đầu code nào
![alt text](https://i.imgur.com/ipttALc.png)

Ở ảnh trên, đoạn code dễ đến mức chắc bạn đang cười mình lol =))

Đầu tiên bạn cần:
- Import Flask từ lib flask đã cài trước
- Khởi tạo một instance app bằng constructor `Flask(__name__)`
- Define 1 function với decorator `@app.route('url')`
- Cuối cùng là chạy ứng dụng, public port là 5000

```shell
    # run and see result
    python app.py
```

### Hello Nam dễ quá rồi, có gì hay ho hơn không?
Ummmm, thế mình cùng là thêm 1 demo nhỏ nhỏ nữa, viết 1 API có thể upload được multipart/form-data và save nó trong Database nhé?

# Hết Hello Nam, giờ đến Upload multipart/form-data
Ở đây mình sử dụng MySQL làm Database, vì thế đầu tiên mình phải làm đó là cài đặt lib flask-mysql để có thể thao tác được với hệ CSDL này.

```shell
    pip install Flask-MySQLdb
```


![alt text](https://i.imgur.com/4YunD8e.png)
Tiếp theo, khởi tạo một instance mysql để thực hiên truy vấn:
- Import MySql từ flask_mysqldb
- Init instance mysql từ constructor `MySQL(app)`
- Config connection đến MySQL

Tiếp theo, mình tiến hàng code function `upload_form_data`

![alt text](https://i.imgur.com/iPQfUCd.png)
Đầu tiền:
- Define một function với url là `/upload-form-data`
- Lấy ra request.form có key là `file`
- Kiểm tra file có tồn tại không, sau đó save vào disk

Lưu ý: key `file` ở đây chỉ là key trong form-data, có thể thay thế bằng những tên khác đều được, dưới chính là ví dụ mình gọi từ `Postman`
![](https://i.imgur.com/BKZLxr6.png)


Ok, như vậy là mình đã lấy được file trong form-data thông qua `request.files['key']`, save được file vào disk, vậy còn text trong form-data thì lấy thế nào?
 
Đơn giản thôi, chúng ta chỉ cần lấy từ `request.form['key']` là xong, dễ quá phải không nào =)))
![](https://i.imgur.com/WRgeXPv.png)

Tóm váy, thế là mình đã lấy được cả file, cả text trong form-data, vì thế giờ chỉ cần save vào MySQL là done, quá dễ.
```
form-data chỉ chứa 2 type: file/text, không tin mở Postman, chọn body > form-data > select thử chỗ values mà xem
```


![](https://i.imgur.com/hJiFNrY.png)
Ok full không che cả source code upload form-data lun ~~


Hầy, như vậy là mình đã hoàn thành một demo nhỏ sử dụng flask để viết api upload multipart/form-data.
Có thể thấy, việc sử dụng Flask để tạo ra 1 api là hết sức dễ dàng(so với các `framework` khác), thì ở đây, mình chỉ cần cài cắm các library vào, init các instance của thư viện đó, cài cắm config các parameters cần thiết, rồi viết logic thôi, không cần quan tâm nhiều, hehe.

Happy coding!

Nam.


[wsgi]: https://wsgi.readthedocs.io/en/latest/
[microframework]: https://en.wikipedia.org/wiki/Microframework