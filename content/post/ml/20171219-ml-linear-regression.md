---
title: "[ML] Hồi quy tuyến tính (Linear Regression)"
slug: ml-linear-regression
date: 2017-12-19T12:54:51+09:00
categories:
- Học Máy
- ML
tags:
- Học Máy
keywords:
- Học Máy
- Machine Learning
- Linear Regression
autoThumbnailImage: true
thumbnailImagePosition: left
thumbnailImage: https://res.cloudinary.com/dominhhai/image/upload/dl/logo.png
metaAlignment: center
customJS:
- https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.1/Chart.min.js
---
Học có giám sát (*Supervised Learning*) được chia ra làm 2 dạng lớn là **hồi quy** (*regression*) và **phân loại** (*classification*) dựa trên tập dữ liệu mẫu - tập huấn luyện (*training data*). Với bài đầu tiên này ta sẽ bắt đầu bằng bài toán hồi quy mà cụ thể là hồi quy tuyến tính (*linear regression*).
<!--more-->

<!--toc-->

# 1. Định nghĩa
Mục tiêu của giải thuật hồi quy tuyến tính là dự đoán giá trị của một hoặc nhiều *biến mục tiêu liên tục* (*continuous target variable*) $y$ dựa trên một véc-to đầu vào $\mathbf{x}$.

Ví dụ: dự đoán giá nhà ở Hà Nội dựa vào thông tin về diện tích, vị trí, năm xây dựng của ngôi nhà thì $t$ ở đây sẽ là giá nhà và $\mathbf{x}=(x_1,x_2,x_3)$ với $x_1$ là diện tích, $x_2$ là vị trí và $x_3$ là năm xây dựng.

Nếu bạn còn nhớ thì đây chính là phương pháp <a href="https://en.wikipedia.org/wiki/Regression_analysis" target="_blank"_ rel="noopener noreferrer">phân tích hồi quy</a> của xác suất thống kê. Mọi lý thuyết cơ bản của phương pháp này vẫn được giữa nguyên nhưng khi áp dụng cho máy tính thì về mặt cài đặt có thay đổi đôi chút.

Về cơ bản thì ta sẽ có một tập huấn luyện chứa các cặp $(\mathbf{x}^{(i)},y^{(i)})$ tương ứng và nhiệm vụ của ta là phải tìm giá trị $\hat{y}$ ứng với một đầu vào $\mathbf{x}$ mới. Để làm điều này ta cần tìm được quan hệ giữa $\mathbf{x}$ và $y$ để từ đó đưa ra được dự đoán. Hay nói cách trừu tượng hơn là ta cần vẽ được một đường quan hệ thể hiện mối quan hệ trong tập dữ liệu.

<canvas id="ex1"></canvas>

Như hình minh họa phía trên thì ta có thể vẽ được một đường màu xanh `y=3+4x` để thể hiện quan hệ giữa `x` và `y` dựa vào các điểm dữ liệu huấn luyện đã biết. Thuật toán hồi quy tuyến tính sẽ giúp ta tự động tìm được đường màu xanh đó để từ đó ta có thể dự đoán được `y` cho một `x` chưa từng xuất hiện bao giờ.

> Lưu ý về kí hiệu: xem danh sách kí hiệu <a href="/vi/2017/10/math-notation/" target="_blank"_>tại đây</a>.

# 2. Mô hình
Mô hình đơn giản nhất là mô hình kết hợp tuyến tính của các biến đầu vào:
$$y(\mathbf{x},\theta)=\theta_0+\theta_1x_1+...+\theta\_{k-1}x\_{k-1} ~~~(2.1)$$
trong đó $\mathbf{x}\in\\mathbb{R}^{k-1}$ là véc-to biến đầu vào và $\theta\in\\mathbb{R}^k$ là véc-to trọng số tương ứng. Thường $\theta$ được gọi là tham số của mô hình. Giá trị của tham số sẽ được ước lượng bằng cách sử dụng các cặp giá trị $(\mathbf{x}^{(i)},y^{(i)})$ của tập huấn luyện.

Thực ra mô hình tuyến tính là chỉ cần ở mức tuyến tính giữa tham số $\theta$ và $y$ là đủ. Và mình cho rằng tên gọi tuyến tính là xuất phát giữa $\theta$ và $y$, chứ không phải giữa $\mathbf{x}$ và $y$. Nói cách khác, ta có thể kết hợp các $\mathbf{x}$ một cách phi tuyến trước khi hợp với $\theta$ để được $y$. Một cách đơn giản là sử dụng hàm phi tuyến cho $\mathbf{x}$ như sau:
$$y(\mathbf{x},\theta)=\theta_0+\theta_1\phi_1(\mathbf{x})+...+\theta\_{n-1}\phi\_{n-1}(\mathbf{x}) ~~~(2.2)$$

$\theta_0$ được gọi là **độ lệch** (*bias*) nhằm cắt đi mức độ chênh lệch giữa mô hình và thực tế. Các hàm phi tuyến $\phi_i(\mathbf{x})$ này được gọi là các **hàm cơ bản** (*basic function*). Thường người ta sẽ đặt $\phi_0(\mathbf{x})=1$ và viết lại công thức trên như sau:
$$y(\mathbf{x},\theta)=\sum\_{i=0}^{n-1}\theta_i\phi_i(\mathbf{x})=\theta^{\intercal}\phi(\mathbf{x}) ~~~(2.3)$$

Như <a href="/vi/2017/10/math-notation/#s%E1%BB%91-v%C3%A0-ma-tr%E1%BA%ADn" target="_blank"_>quy ước</a> thì tất cả các véc-to nếu không nói gì thì ta ngầm định với nhau rằng nó là véc-to cột nên ta có được cách viết nhân ma trận như trên.

# 3. Chọn hàm cơ bản
Việc chọn hàm cơ bản $\phi(\mathbf{x})$ cũng chính là chọn tính năng cho đầu vào rất quan trọng trong học máy. Ngoài ra việc chọn ra sao còn ảnh hưởng tới tốc độ và bộ nhớ để tính toán nữa. Ở đây tôi chỉ để cập tới 1 vài cách đơn giản để chọn hàm cơ bản mà thôi.

## 3.1. Giữ nguyên đầu vào
Giữ nguyên đầu vào có ý là không thay đổi giá trị đầu vào, tức:
$$\phi(\mathbf{x})=\mathbf{x}$$

Thường người ta sẽ gom các đầu vào thành một ma trận $\mathbf{X}\in\mathbb{R}^{m\times n}$:
$$\mathbf{X}=[\mathbf{x}_1,\mathbf{x}_2,...,\mathbf{x}_m]^{\intercal}$$

Mỗi hàng của ma trận chứa 1 mẫu và mỗi cột sẽ chứa các thuộc tính đầu vào.

## 3.2. Chuẩn hoá đầu vào
Là phương pháp co giãn các thuộc tính về khoảng $[min,max]$ nào đó (thường là $[-1,1]$ hoặc $[-0.5,0.5]$) dựa vào kì vọng và độ lệch chuẩn của chúng.
$$x_i=\frac{x_i-\mu_i}{s_i}$$

Trong đó, $\mu_i$ là trung bình, còn $s_i$ là độ lệch chuẩn của tính năng $i$. Đôi lúc người ta cũng có thể lấy $s_i$ là khoảng rộng chuẩn $s_i=max-min$.

Việc này không làm mất tính chất phân phối của chúng nên không ảnh hưởng tới kết quả học. Nhưng lại giúp cho việc học trở lên dễ dàng hơn vì các thuộc tính gần như cùng khoảng nhỏ với nhau. Phương pháp này còn có tên khác là **chuẩn hoá trung bình** (*mean normalization*).

## 3.3. Đa thức hoá
Sử dụng đa thức bậc cao để làm đầu vào:
$$\phi_i(\mathbf{x})=\mathbf{x}^i$$

Với các bài toán hồi quy tuyến tính thì phương pháp này rất hay được sử dụng.

## 3.4. Sử dụng hàm Gaussian
$$\phi_i(\mathbf{x})=\exp\Bigg(-\frac{(\mathbf{x}-\mu_i)^2}{2s^2}\Bigg)$$

$\mu_i$ ở đây sẽ chỉ định vị trí trung bình cho đầu vào còn $s$ sẽ chỉ định độ phân tán cho đầu vào. Việc sử dụng hàm này sẽ giúp ta có được đầu vào theo phân phối chuẩn.

## 3.5. Sử dụng hàm sigmoid
Tương tự như hàm Gaussian, ta có thể sử dụng hàm sigmoid để biến đổi đầu vào:
$$\phi_i(\mathbf{x})=\sigma\Bigg(-\frac{\mathbf{x}-\mu_i}{s}\Bigg)$$

Hàm sigmoid được sử dụng là sigmoid chuẩn:
$$\sigma(z)=\frac{1}{1+\exp(-z)}$$

Một biến thể khác là sử dụng $tanh$ vì nó khá gần với $sigmoid$:
$$tanh(z)=2\sigma(2z)-1$$

# 4. Ước lượng tham số
Giả sử ta có $m$ cặp dữ liệu huấn luyện $(\mathbf{x}_i, y_i)~~~,i=\overline{1,m}$ được tổ chức tương ứng bằng $\mathbf{X}=[\mathbf{x}_1,\mathbf{x}_2,...,\mathbf{x}_m]^{\intercal}, \mathbf{y}=[y_1,y_2,...,y_m]^{\intercal}$ và $\mathbf{\hat{y}}\in\mathbb{R}^m$ là kết quả dự đoán tương ứng. Ta có thể đánh giá mức độ chênh lệch kết quả $\hat{y}$ và $y$ bằng một **hàm lỗi** (*lost function*) như sau:

$$
\begin{aligned}
J(\theta) &= \frac{1}{2m}\sum\_{i=1}^m(\hat{y}_i-y_i)^2
\\cr\ &= \frac{1}{2m}\sum\_{i=1}^m\Big(\theta^{\intercal}\phi(\mathbf{x}_i)-y_i\Big)^2 &(3.1)
\end{aligned}
$$

Công thức trên thể hiện trung bình của độ lệch (*khoảng cách*) giữa các điểm dữ liệu thực tế và kết quả dự đoán sau khi ta ước lượng tham số. Còn tại sao ta lại chia cho 2 thì tôi sẽ giải thích sau. Hàm lỗi còn có tên gọi khác là **hàm lỗi bình phương** (*squared error function*) hoặc **hàm lỗi trung bình bình phương** (*mean squared error function*) hoặc **hàm chi phí** (*cost function*).

Không cần giải thích ta cũng có thể hiểu với nhau rằng tham số tốt nhất là tham số giúp cho hàm lỗi $J$ đạt giá trị nhỏ nhất.
$$\hat\theta=\arg\min_{\theta}J(\theta) ~~~(3.2)$$
Kết quả tối ưu nhất là $\mathbf{\hat{y}}=\mathbf{y}$, tức là $J(\theta)=0$. Để giải quyết bài toán này ta có thể sử dụng đạo hàm của $J(\theta)$ và tìm $\theta$ sao cho $J(\theta)^{\prime}=0$.
$$
\begin{aligned}
\ &0 = \frac{1}{m}\sum\_{i=1}^m(\theta^{\intercal}\phi(\mathbf{x}_i)-y_i)\phi(\mathbf{x}_i)^{\intercal} &(3.3)
\\cr\iff& \sum\_{i=1}^m\theta^{\intercal}\phi(\mathbf{x}_i)\phi(\mathbf{x}_i)^{\intercal} = \sum\_{i=1}^my_i\phi(\mathbf{x}_i)^{\intercal}&(3.4)
\\cr\iff&\theta = (\Phi^{\intercal}\Phi)^{-1}\Phi^{\intercal}\mathbf{y} &(3.5)
\end{aligned}
$$

Đây chính là **công thức chuẩn** (*normal equation*) của bài toán ta cần giải. Trong đó ma trận $\Phi\in\\mathbb{R}^{m\times n}$ được gọi là **ma trận mẫu** (*design matrix*), ta có thể hiểu nó đơn giản là tập mẫu của ta:
$$\Phi=
\begin{bmatrix}
\phi_0(\mathbf{x}_1)&\phi_1(\mathbf{x}_1)&...&\phi\_{n-1}(\mathbf{x}_1)\\cr
\phi_0(\mathbf{x}_2)&\phi_1(\mathbf{x}_2)&...&\phi\_{n-1}(\mathbf{x}_2)\\cr
\vdots&\vdots&\ddots&\vdots\\cr
\phi_0(\mathbf{x}_m)&\phi_1(\mathbf{x}_m)&...&\phi\_{n-1}(\mathbf{x}_m)
\end{bmatrix}
$$

Để ý rằng ở ma trận $\Phi$ ta sắp mỗi dữ liệu huấn luyện theo hàng ($m$ hàng) và các thuộc tính của chúng theo cột ($n$ cột). Các thuộc tính ở đây được biến đổi bằng các hàm $\phi_i(\mathbf{x}_j)$. Và lưu ý rằng như đã đặt phía trên $\phi_0(\mathbf{x_j}) = 1$ với mọi $j=\overline{1,m}$.

Ở phép lấy đạo hàm `(3.3)` ta thấy rằng mẫu số 2 bị triệt tiêu và giúp bỏ đi được thừa số 2 khi tính đạo hàm. Đấy chính là lý do mà người ta để mẫu số 2 cho hàm lỗi.

# 5. Lập trình
Chém gió loằng ngoằng mãi rồi, giờ phải bắt tay vào code thử xem đúng hay sai.
## 5.1. Ví dụ 1
Ví dụ khởi động này tôi sẽ lấy dữ liệu đơn giản $y=3+4x$ để làm việc.
Trước tiên tôi đã chuẩn bị tập dữ liệu huấn luyện gồm 100 cặp dữ liệu được sinh ra theo nhiễu của hàm $y=3+4x$ tại <a href="https://github.com/dominhhai/mldl/blob/master/dataset/1_linearinput.csv" target="_blank"_ rel="noopener noreferrer">Repo trên Github</a>.

Ở đây tôi sẽ sử dụng các thư viện `pandas` (xử lý dữ liệu), `mathplotlib` (đồ hình dữ liệu) và `numpy` (thao tác toán học) để làm việc:
{{< codeblock "one_var_linearinput.py" "python" "https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py">}}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
{{< /codeblock >}}

Dữ liệu của ta chỉ có 1 chiều nên dễ dàng đồ hình hoá, việc này cũng giúp ta ước lượng được đôi chút việc chọn các tiêu chí ràng buộc cho mô hình của ta.
{{< codeblock "one_var_linearinput.py" "python" "https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py">}}
# load data
df = pd.read_csv(DATA_FILE_NAME)
# plot data
df.plot(x='x', y='y', legend=False, marker='o', style='o', mec='b', mfc='w')
# expected line
plt.plot(df.values[:,0], df.values[:,1], color='g')
plt.xlabel('x'); plt.ylabel('y'); plt.show()

# extract X, y
X = df.values[:,0]
y = df.values[:,2]
# number of training examples
m = y.size
{{< /codeblock >}}

<canvas id="ex2"></canvas>

Nhìn vào biểu đồ của dữ liệu ta có thể nghĩ rằng $x$ ở đây tuyến tính với $y$, tức là ta có thể chọn $\Phi\in\mathbb{R}^{m\times 2}$ với $\phi_1(\mathbf{x_j})=\mathbf{x_j}$. Lúc này ta sẽ có:
$$\Phi=
\begin{bmatrix}
1&\mathbf{x}_1\\cr
1&\mathbf{x}_2\\cr
\vdots&\vdots&\\cr
1&\mathbf{x}_m\\cr
\end{bmatrix}
$$
Như vậy $\Phi$ lúc này đơn giản là bằng với ma trận $\mathbf{X}$ có thêm cột $1$ ở đầu:
$$\Phi=\mathbf{X}=
\begin{bmatrix}
1&x_1\\cr
1&x_2\\cr
\vdots&\vdots&\\cr
1&x_m\\cr
\end{bmatrix}
$$

Lúc này ta có thể viết lại $y$ như sau:
$$y=\theta^{\intercal}\mathbf{X}$$

{{< codeblock "one_var_linearinput.py" "python" "https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py">}}
X = np.concatenate((np.ones((m,1)), X.reshape(-1,1)), axis=1)
{{< /codeblock >}}

Do $(\Phi^{\intercal}\Phi)$ có thể không khả nghịch nên ta có thể sử dụng phép giả nghịch đảo để làm việc:
{{< codeblock "one_var_linearinput.py" "python" "https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py">}}
theta = np.dot(np.linalg.pinv(np.dot(X.T, X)), np.dot(X.T, y))
{{< /codeblock >}}

Phép trên sẽ cho ta kết quả: `theta=[-577.17310612, 4.16001358]`, tức:
$$
\begin{cases}
\theta_0=-577.17310612\\cr
\theta_1=4.16001358
\end{cases}
$$
Giờ ta sẽ tính kết quả ước lượng và mô phỏng lên hình vẽ xem sao.
{{< codeblock "one_var_linearinput.py" "python" "https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py">}}
y_hat = np.dot(X, theta)
plt.plot(df.values[:,0], y_hat, color='r')
plt.xlabel('x'); plt.ylabel('y'); plt.show()
{{< /codeblock >}}

<canvas id="ex3"></canvas>

Ở hình trên, đường màu xanh là đường mà ta mong muốn đạt được còn đường màu đỏ mà mô hình ước lượng được.  Như vậy ta thấy rằng $\theta_1$ khá khớp còn $\theta_0$ lại lệch rất nhiều, nhưng kết quả lại khá khớp với tập dữ liệu đang có. Nên ta có thể kì vọng rằng nếu gia tăng khoảng dữ liệu thì công thức chuẩn sẽ cho ta kết quả hợp lý hơn.

Toàn bộ mã nguồn đầy đủ của phần này tôi có để trên Github và ngoài ra còn thêm các phần thực hiện bằng thư viện `scikit-learn` và `TensorFlow` để kiểm chứng kết quả đạt được nữa. Cụ thể bạn có xem trên <a href="https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.py" target="_blank"_ rel="noopener noreferrer">Github</a> hoặc dễ dàng hơn với <a href="https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_linearinput.ipynb" target="_blank"_ rel="noopener noreferrer">IPython</a>.

## 5.2. Ví dụ 2
Phần này ta sẽ phức tạp vấn đề lên 1 chút bằng cách lấy $y=\sin(2\pi x)$. Tương tự như trên tôi đã chuẩn bị dữ liệu trên <a href="https://github.com/dominhhai/mldl/blob/master/dataset/1_sin2pi.csv" target="_blank"_ rel="noopener noreferrer">trên Repo đó</a>. Cụ thể nó sẽ được mô phỏng như sau:

<canvas id="ex4"></canvas>

Thấy đường loằng ngoằng kia ta có thể dự đoán rằng nó được tạo bởi đa thức của các biến $x$ như sau:
$$y=\theta_0+\theta_1x+\theta_2x^2+...+\theta_nx^n$$

Hay nói cách khác thì $\phi_i(\mathbf{x_j})=\mathbf{x_j}^j$. Ở đây ta chọn lấy $n=3$, ta sẽ có:
$$\Phi=
\begin{bmatrix}
1&\mathbf{x}_1&\mathbf{x}_1^2&\mathbf{x}_1^3\\cr
1&\mathbf{x}_2&\mathbf{x}_2^2&\mathbf{x}_2^3\\cr
\vdots&\vdots&\vdots&\vdots&\\cr
1&\mathbf{x}_m&\mathbf{x}_m^2&\mathbf{x}_m^3\\cr
\end{bmatrix}$$

Do $\mathbf{x}$ chỉ có 1 phần tử $x$ nên ta có thể viết lại $\Phi$ như sau:
$$\Phi=
\begin{bmatrix}
1&x_1&x_1^2&x_1^3\\cr
1&x_2&x_2^2&x_2^3\\cr
\vdots&\vdots&\vdots&\vdots&\\cr
1&x_m&x_m^2&x_m^3\\cr
\end{bmatrix}$$

Từ đây ta có thể tính được $\theta=[-0.18829466, 12.07512913, -35.05777051, 23.38720567]$, hay:
$$
\begin{cases}
\theta_0=-0.18829466\\cr
\theta_1=12.07512913\\cr
\theta_2=-35.05777051\\cr
\theta_3=23.38720567
\end{cases}
$$

Kết quả ước lượng có thể được mô hình hoá như sau:

<canvas id="ex5"></canvas>

Kết quả lần này cũng khá khớp với dữ liệu mẫu mà ta có được. Nếu bạn quan tâm tới mã nguồn cụ thể thì xem  trên <a href="https://github.com/dominhhai/mldl/blob/master/code/linear_regression/one_var_sin2pi.py" target="_blank"_ rel="noopener noreferrer">Github</a> nhé.

# 6. Kết luận
Thuật toán hồi quy tuyến tính (*linear regression*) thuộc vào nhóm học có giám sát (*supervised learning*) là được **mô hình** hoá bằng:
$$y(\mathbf{x},\theta)=\theta^{\intercal}\phi(\mathbf{x})$$
Khi khảo sát tìm tham số của mô hình ta có thể giải quyết thông qua việc tối thiểu hoá **hàm lỗi** (*loss function*):
$$J(\theta)=\dfrac{1}{2m}\displaystyle\sum\_{i=1}^m\Big(\theta^{\intercal}\phi(\mathbf{x}_i)-y_i\Big)^2$$
Hàm lỗi này thể hiện trung bình độ lệch giữa kết quả ước lượng và kết quả thực tế. Việc lấy bình phương giúp ta có thể dễ dàng tối ưu được bằng cách lấy đạo hàm vì nó có đạo hàm tại mọi điểm! Qua phép đạo hàm ta có được **công thức chuẩn** (*normal equation*) cho tham số:
$$\hat\theta = (\Phi^{\intercal}\Phi)^{-1}\Phi^{\intercal}\mathbf{y}$$
Trong đó $\Phi\in\\mathbb{R}^{m\times n}$:
$$\Phi=
\begin{bmatrix}
\phi_0(\mathbf{x}_1)&\phi_1(\mathbf{x}_1)&...&\phi\_{n-1}(\mathbf{x}_1)\\cr
\phi_0(\mathbf{x}_2)&\phi_1(\mathbf{x}_2)&...&\phi\_{n-1}(\mathbf{x}_2)\\cr
\vdots&\vdots&\ddots&\vdots\\cr
\phi_0(\mathbf{x}_m)&\phi_1(\mathbf{x}_m)&...&\phi\_{n-1}(\mathbf{x}_m)
\end{bmatrix}
$$

Khi lập trình với `python` ta có thể giải quyết việc $(\Phi^{\intercal}\Phi)$ không khả nghịch bằng cách sử dụng **giả nghịch đảo** để tính toán:
```python
np.linalg.pinv(np.dot(X.T, X))
```

Mặc dù công thức chuẩn có thể tính được tham số nhưng với tập dữ liệu mà lớn thì khả năng sẽ không khít được với bộ nhớ của máy tính, nên trong thực tế người ta thường sử dụng phương pháp đạo hàm để tối ưu. Vậy phương pháp này là gì thì ta sẽ cùng xem xét ở bài viết sau.

<script>
function fnMain() {
  var opts = {
    title: {
      display: true,
      position: 'bottom',
      text: 'Hình 1. Quan hệ y=3+4x'
    },
    scales: {
      xAxes: [{
        type: 'linear',
        position: 'bottom',
        ticks: {
          max: 5000
        },
        scaleLabel: {
          display: true,
          labelString: 'x'
        }
      }],
      yAxes: [{
        scaleLabel: {
          display: true,
          labelString: 'y'
        }
      }]
    }
  };
  // ex1: y=3+4x
  new Chart('ex1', {
    type: 'line',
    data: {
      datasets: [{
        label: 'Expected Line',
        backgroundColor: 'rgba(0, 128, 0, 1)',
        borderColor: 'rgba(0, 128, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:1024,y:4099},{x:4968,y:19875}]
      }, {
        type: 'bubble',
        label: 'Training Data',
        backgroundColor: 'rgba(54, 162, 235, 0.2)',
        borderColor: 'rgba(54, 162, 235, 1)',
        data: [{x:1024,y:3041.34479672},{x:1077,y:3848.11290461},{x:1093,y:1425.14949365},{x:1114,y:3784.23651747},{x:1125,y:6153.79172866},{x:1199,y:3554.11797412},{x:1228,y:3580.0545406},{x:1254,y:3302.36707498},{x:1271,y:4411.08028247},{x:1300,y:4984.36490397},{x:1339,y:3618.27841285},{x:1377,y:3504.57835306},{x:1417,y:6589.78692837},{x:1536,y:5217.89629577},{x:1613,y:18396.38637446},{x:1636,y:5222.87799369},{x:1665,y:4999.52100524},{x:1680,y:5665.26113175},{x:1726,y:8264.58318873},{x:1726,y:8507.36647557},{x:1844,y:7770.4563142},{x:1884,y:9743.47131578},{x:1942,y:6771.49886413},{x:1962,y:9084.57940426},{x:1994,y:9043.94701116},{x:2007,y:8776.39151486},{x:2066,y:10050.1578826},{x:2239,y:8916.42555485},{x:2239,y:8165.64808713},{x:2305,y:8755.25366519},{x:2310,y:8364.34661322},{x:2322,y:8915.34859433},{x:2379,y:9197.38241427},{x:2440,y:7851.95904396},{x:2544,y:8207.20804011},{x:2549,y:8709.82913985},{x:2578,y:11755.6163048},{x:2604,y:8596.51192103},{x:2615,y:10361.0047482},{x:2638,y:9411.6197175},{x:2761,y:9319.6415672},{x:2769,y:12842.8819172},{x:2786,y:11531.2407491},{x:2820,y:8595.93288139},{x:2844,y:12229.2987937},{x:2876,y:12759.8926708},{x:3055,y:12137.8405953},{x:3068,y:11996.5109723},{x:3094,y:11800.2729985},{x:3094,y:12566.2939954},{x:3130,y:13600.3942388},{x:3132,y:14640.825237},{x:3193,y:11428.4092551},{x:3207,y:12301.4877344},{x:3322,y:12999.5669428},{x:3372,y:15054.715276},{x:3380,y:12525.3570879},{x:3455,y:14183.6408623},{x:3468,y:16607.8764098},{x:3549,y:15966.3032046},{x:3605,y:15479.7197159},{x:3696,y:16868.0934864},{x:3704,y:13931.9144869},{x:3939,y:16177.9194112},{x:3940,y:14843.2978469},{x:3954,y:17660.6273388},{x:4001,y:11626.150281},{x:4037,y:18021.1539509},{x:4040,y:15692.8771098},{x:4072,y:15529.7418794},{x:4110,y:19643.2255653},{x:4129,y:15436.0340749},{x:4161,y:16222.9260112},{x:4174,y:15331.25712},{x:4240,y:15681.3275478},{x:4291,y:19263.7251749},{x:4298,y:16254.3605446},{x:4327,y:16945.6846065},{x:4364,y:18965.7091401},{x:4420,y:17848.2934953},{x:4422,y:18893.8296387},{x:4441,y:19548.8303537},{x:4449,y:17997.3169139},{x:4509,y:17680.3094298},{x:4535,y:20888.1014948},{x:4559,y:18718.2694955},{x:4626,y:17537.3976187},{x:4710,y:18955.2873097},{x:4737,y:16783.8474195},{x:4776,y:19772.5909007},{x:4786,y:17760.8922801},{x:4809,y:20106.9348223},{x:4813,y:19525.5150033},{x:4845,y:17059.5073393},{x:4860,y:19594.6604656},{x:4884,y:19585.7409977},{x:4901,y:18295.7299998},{x:4949,y:21835.5489789},{x:4949,y:19998.4766695},{x:4968,y:20582.4548821}]
      }]
    },
    options: opts
  });
  // ex2: y=3+4x
  opts.title.text = 'Hình 2. Tập huấn luyện y=3+4x';
  new Chart('ex2', {
    type: 'line',
    data: {
      datasets: [{
        label: 'Expected Line',
        backgroundColor: 'rgba(0, 128, 0, 1)',
        borderColor: 'rgba(0, 128, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:1024,y:4099},{x:4968,y:19875}]
      }, {
        type: 'bubble',
        label: 'Training Data',
        backgroundColor: 'rgba(54, 162, 235, 0.2)',
        borderColor: 'rgba(54, 162, 235, 1)',
        data: [{x:1024,y:3041.34479672},{x:1077,y:3848.11290461},{x:1093,y:1425.14949365},{x:1114,y:3784.23651747},{x:1125,y:6153.79172866},{x:1199,y:3554.11797412},{x:1228,y:3580.0545406},{x:1254,y:3302.36707498},{x:1271,y:4411.08028247},{x:1300,y:4984.36490397},{x:1339,y:3618.27841285},{x:1377,y:3504.57835306},{x:1417,y:6589.78692837},{x:1536,y:5217.89629577},{x:1613,y:18396.38637446},{x:1636,y:5222.87799369},{x:1665,y:4999.52100524},{x:1680,y:5665.26113175},{x:1726,y:8264.58318873},{x:1726,y:8507.36647557},{x:1844,y:7770.4563142},{x:1884,y:9743.47131578},{x:1942,y:6771.49886413},{x:1962,y:9084.57940426},{x:1994,y:9043.94701116},{x:2007,y:8776.39151486},{x:2066,y:10050.1578826},{x:2239,y:8916.42555485},{x:2239,y:8165.64808713},{x:2305,y:8755.25366519},{x:2310,y:8364.34661322},{x:2322,y:8915.34859433},{x:2379,y:9197.38241427},{x:2440,y:7851.95904396},{x:2544,y:8207.20804011},{x:2549,y:8709.82913985},{x:2578,y:11755.6163048},{x:2604,y:8596.51192103},{x:2615,y:10361.0047482},{x:2638,y:9411.6197175},{x:2761,y:9319.6415672},{x:2769,y:12842.8819172},{x:2786,y:11531.2407491},{x:2820,y:8595.93288139},{x:2844,y:12229.2987937},{x:2876,y:12759.8926708},{x:3055,y:12137.8405953},{x:3068,y:11996.5109723},{x:3094,y:11800.2729985},{x:3094,y:12566.2939954},{x:3130,y:13600.3942388},{x:3132,y:14640.825237},{x:3193,y:11428.4092551},{x:3207,y:12301.4877344},{x:3322,y:12999.5669428},{x:3372,y:15054.715276},{x:3380,y:12525.3570879},{x:3455,y:14183.6408623},{x:3468,y:16607.8764098},{x:3549,y:15966.3032046},{x:3605,y:15479.7197159},{x:3696,y:16868.0934864},{x:3704,y:13931.9144869},{x:3939,y:16177.9194112},{x:3940,y:14843.2978469},{x:3954,y:17660.6273388},{x:4001,y:11626.150281},{x:4037,y:18021.1539509},{x:4040,y:15692.8771098},{x:4072,y:15529.7418794},{x:4110,y:19643.2255653},{x:4129,y:15436.0340749},{x:4161,y:16222.9260112},{x:4174,y:15331.25712},{x:4240,y:15681.3275478},{x:4291,y:19263.7251749},{x:4298,y:16254.3605446},{x:4327,y:16945.6846065},{x:4364,y:18965.7091401},{x:4420,y:17848.2934953},{x:4422,y:18893.8296387},{x:4441,y:19548.8303537},{x:4449,y:17997.3169139},{x:4509,y:17680.3094298},{x:4535,y:20888.1014948},{x:4559,y:18718.2694955},{x:4626,y:17537.3976187},{x:4710,y:18955.2873097},{x:4737,y:16783.8474195},{x:4776,y:19772.5909007},{x:4786,y:17760.8922801},{x:4809,y:20106.9348223},{x:4813,y:19525.5150033},{x:4845,y:17059.5073393},{x:4860,y:19594.6604656},{x:4884,y:19585.7409977},{x:4901,y:18295.7299998},{x:4949,y:21835.5489789},{x:4949,y:19998.4766695},{x:4968,y:20582.4548821}]
      }]
    },
    options: opts
  });
  // ex3: y=3+4x
  opts.title.text = 'Hình 3. Kết quả dự đoán y=3+4x';
  new Chart('ex3', {
    type: 'line',
    data: {
      datasets: [{
        label: 'Expected Line',
        backgroundColor: 'rgba(0, 128, 0, 1)',
        borderColor: 'rgba(0, 128, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:1024,y:4099},{x:4968,y:19875}]
      }, {
        label: 'Predicted Line',
        backgroundColor: 'rgba(255, 0, 0, 1)',
        borderColor: 'rgba(255, 0, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:1024,y:3682.68080091},{x:4968,y:20089.77436472}]
      }, {
        type: 'bubble',
        label: 'Training Data',
        backgroundColor: 'rgba(54, 162, 235, 0.2)',
        borderColor: 'rgba(54, 162, 235, 1)',
        data: [{x:1024,y:3041.34479672},{x:1077,y:3848.11290461},{x:1093,y:1425.14949365},{x:1114,y:3784.23651747},{x:1125,y:6153.79172866},{x:1199,y:3554.11797412},{x:1228,y:3580.0545406},{x:1254,y:3302.36707498},{x:1271,y:4411.08028247},{x:1300,y:4984.36490397},{x:1339,y:3618.27841285},{x:1377,y:3504.57835306},{x:1417,y:6589.78692837},{x:1536,y:5217.89629577},{x:1613,y:18396.38637446},{x:1636,y:5222.87799369},{x:1665,y:4999.52100524},{x:1680,y:5665.26113175},{x:1726,y:8264.58318873},{x:1726,y:8507.36647557},{x:1844,y:7770.4563142},{x:1884,y:9743.47131578},{x:1942,y:6771.49886413},{x:1962,y:9084.57940426},{x:1994,y:9043.94701116},{x:2007,y:8776.39151486},{x:2066,y:10050.1578826},{x:2239,y:8916.42555485},{x:2239,y:8165.64808713},{x:2305,y:8755.25366519},{x:2310,y:8364.34661322},{x:2322,y:8915.34859433},{x:2379,y:9197.38241427},{x:2440,y:7851.95904396},{x:2544,y:8207.20804011},{x:2549,y:8709.82913985},{x:2578,y:11755.6163048},{x:2604,y:8596.51192103},{x:2615,y:10361.0047482},{x:2638,y:9411.6197175},{x:2761,y:9319.6415672},{x:2769,y:12842.8819172},{x:2786,y:11531.2407491},{x:2820,y:8595.93288139},{x:2844,y:12229.2987937},{x:2876,y:12759.8926708},{x:3055,y:12137.8405953},{x:3068,y:11996.5109723},{x:3094,y:11800.2729985},{x:3094,y:12566.2939954},{x:3130,y:13600.3942388},{x:3132,y:14640.825237},{x:3193,y:11428.4092551},{x:3207,y:12301.4877344},{x:3322,y:12999.5669428},{x:3372,y:15054.715276},{x:3380,y:12525.3570879},{x:3455,y:14183.6408623},{x:3468,y:16607.8764098},{x:3549,y:15966.3032046},{x:3605,y:15479.7197159},{x:3696,y:16868.0934864},{x:3704,y:13931.9144869},{x:3939,y:16177.9194112},{x:3940,y:14843.2978469},{x:3954,y:17660.6273388},{x:4001,y:11626.150281},{x:4037,y:18021.1539509},{x:4040,y:15692.8771098},{x:4072,y:15529.7418794},{x:4110,y:19643.2255653},{x:4129,y:15436.0340749},{x:4161,y:16222.9260112},{x:4174,y:15331.25712},{x:4240,y:15681.3275478},{x:4291,y:19263.7251749},{x:4298,y:16254.3605446},{x:4327,y:16945.6846065},{x:4364,y:18965.7091401},{x:4420,y:17848.2934953},{x:4422,y:18893.8296387},{x:4441,y:19548.8303537},{x:4449,y:17997.3169139},{x:4509,y:17680.3094298},{x:4535,y:20888.1014948},{x:4559,y:18718.2694955},{x:4626,y:17537.3976187},{x:4710,y:18955.2873097},{x:4737,y:16783.8474195},{x:4776,y:19772.5909007},{x:4786,y:17760.8922801},{x:4809,y:20106.9348223},{x:4813,y:19525.5150033},{x:4845,y:17059.5073393},{x:4860,y:19594.6604656},{x:4884,y:19585.7409977},{x:4901,y:18295.7299998},{x:4949,y:21835.5489789},{x:4949,y:19998.4766695},{x:4968,y:20582.4548821}]
      }]
    },
    options: opts
  });
  // ex4: y=sin(2πx)
  opts.title.text = 'Hình 4. Tập huấn luyện y=sin(2πx)';
  opts.scales.xAxes[0].ticks = {
    min: 0,
    max: 1
  };
  new Chart('ex4', {
    type: 'line',
    data: {
      datasets: [{
        type: 'bubble',
        label: 'Training Data',
        backgroundColor: 'rgba(54, 162, 235, 0.2)',
        borderColor: 'rgba(54, 162, 235, 1)',
        data: [{x:0.0029817174119300001, y:-0.16607862328799999},{x:0.0040970978991000002, y:0.0098708158100699987},{x:0.060802875311699991, y:0.52682562042400005},{x:0.091589046460899992, y:0.52096596735099998},{x:0.095122973505500003, y:0.55693948823499995},{x:0.10404223774, y:0.71213321193299994},{x:0.10723691203600001, y:0.69094897854600001},{x:0.116083362207, y:0.77520049101199995},{x:0.11751064009000002, y:0.82254265433999996},{x:0.124706389401, y:0.89122412024200004},{x:0.13814181482499999, y:0.8357897651620001},{x:0.14738873420199999, y:1.0286100035100001},{x:0.14967686405200001, y:0.80884968288100001},{x:0.155161234989, y:0.75864165142000006},{x:0.17711196866700002, y:0.86908827240600006},{x:0.18340913565000003, y:0.89268538591399993},{x:0.18887376439100001, y:0.73700428091800008},{x:0.22147238621500001, y:1.04231819518},{x:0.23680135419599999, y:1.1564295722799998},{x:0.24377224763700001, y:1.1200840568100001},{x:0.24406379121899999, y:1.0774348514600001},{x:0.24904163511900002, y:1.2158587435400001},{x:0.25668110709699998, y:1.0224723100199999},{x:0.288576476267, y:0.779368801188},{x:0.30129154007699999, y:0.71708081887399999},{x:0.30533724308499999, y:0.88114014543099994},{x:0.31059601909899998, y:1.08786429857},{x:0.32875730553400001, y:0.63528697671699996},{x:0.329372080884, y:1.0180415090000001},{x:0.34524661634000003, y:0.78319552903300005},{x:0.351845475149, y:0.82464430926999999},{x:0.35194958510700003, y:0.83894558987299994},{x:0.36241674699500004, y:0.74024524606099995},{x:0.36585147028899995, y:0.70185449096200003},{x:0.36831489684399998, y:0.66378369581399999},{x:0.38816079891900002, y:0.61611623223700007},{x:0.40857827966799998, y:0.41789214479300002},{x:0.43232723730599998, y:0.57491556299400004},{x:0.44474377991499997, y:0.29685930976000002},{x:0.456000951205, y:0.32517202423000002},{x:0.468209233153, y:0.40004445012400003},{x:0.47511828206600004, y:-0.123564826118},{x:0.49151115018299996, y:0.183341898078},{x:0.49395610604500001, y:-0.15231906193399999},{x:0.49413997608099997, y:0.0367926647649},{x:0.498953214428, y:0.037741509612500002},{x:0.54033163910699999, y:-0.27144092709500001},{x:0.54748168257899998, y:-0.107793332075},{x:0.56624527103900002, y:-0.36859576177199999},{x:0.57236147176999996, y:-0.327000018896},{x:0.59201337384899999, y:-0.39194779790899997},{x:0.60428597257899996, y:-0.64106955102800001},{x:0.60694890447899996, y:-0.63523859562399998},{x:0.60894686356100003, y:-0.81654814878699999},{x:0.61380114420300003, y:-0.48222487028800004},{x:0.61414988728600006, y:-0.48514128842799997},{x:0.61587224947700003, y:-0.735009179535},{x:0.67996958502200011, y:-0.82229024306199994},{x:0.69542844871099996, y:-1.06955649982},{x:0.70990901025900011, y:-0.81391410155100008},{x:0.71522779773, y:-1.0665997980799999},{x:0.72368730721100005, y:-0.9767992728749999},{x:0.72702435288200007, y:-0.74802596326699999},{x:0.72777639697999996, y:-0.88172644933599997},{x:0.74619439575099999, y:-1.02900145549},{x:0.75103600088200007, y:-0.99835346514599999},{x:0.75289941587699993, y:-1.1934704548399999},{x:0.75691436937500001, y:-1.07840192705},{x:0.76072722293999995, y:-1.0189322191100001},{x:0.76203659162000004, y:-1.09125554863},{x:0.76450799187399998, y:-0.86672403584900004},{x:0.76842339619599997, y:-1.1772112161200001},{x:0.77024168710899998, y:-1.08851040633},{x:0.77714487737600002, y:-0.94303460920900006},{x:0.78727898823599995, y:-0.91232018238500001},{x:0.79947824459099992, y:-0.95772300867100002},{x:0.81894584009499993, y:-0.93580845035400007},{x:0.82360710893900002, y:-0.92101794761400002},{x:0.82920788891200004, y:-1.18043186396},{x:0.83903861300500004, y:-0.93429711519500003},{x:0.84066013086700009, y:-1.0674632531899999},{x:0.86843370098499995, y:-0.95206829674100002},{x:0.87227576642399995, y:-0.545963396416},{x:0.88574864622000005, y:-0.57558252971599999},{x:0.88726759003999989, y:-0.45690446606899998},{x:0.89696161909899996, y:-0.67429568419200003},{x:0.89718166817, y:-0.43462205532100001},{x:0.90013891383699995, y:-0.41296254397600002},{x:0.90252781439300001, y:-0.44431657758900001},{x:0.91279400520699994, y:-0.56750061813999997},{x:0.94415047390899998, y:-0.34870547603500002},{x:0.94415435212900001, y:-0.51550197836800005},{x:0.94524776323400006, y:-0.50698198907300007},{x:0.94766233645500009, y:-0.131712712619},{x:0.95213597163800001, y:-0.24849009312800002},{x:0.95819410818600004, y:-0.306472098586},{x:0.96667265099700006, y:-0.058005464345500003},{x:0.96807248718799999, y:-0.49528400794900002},{x:0.97594639826000007, y:-0.0043999903289099994},{x:0.97765203110099996, y:-0.28296262257999999}]
      }, {
        label: 'Expected Line',
        backgroundColor: 'rgba(0, 128, 0, 1)',
        borderColor: 'rgba(0, 128, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:0.0029817174119300001, y:0.018733587109499999},{x:0.0040970978991000002, y:0.025739982150699999},{x:0.060802875311699991, y:0.37281021257399999},{x:0.091589046460899992, y:0.54422994883199993},{x:0.095122973505500003, y:0.56272226648199997},{x:0.10404223774, y:0.60814099144599998},{x:0.10723691203600001, y:0.62395172746899996},{x:0.116083362207, y:0.66640248525799994},{x:0.11751064009000002, y:0.67306195904799992},{x:0.124706389401, y:0.70580110115099992},{x:0.13814181482499999, y:0.76301876671299995},{x:0.14738873420199999, y:0.79926470714200004},{x:0.14967686405200001, y:0.80782193378599998},{x:0.155161234989, y:0.82764957917999993},{x:0.17711196866700002, y:0.896952304087},{x:0.18340913565000003, y:0.91373907454799996},{x:0.18887376439100001, y:0.927148251506},{x:0.22147238621500001, y:0.983978707387},{x:0.23680135419599999, y:0.99656331618599991},{x:0.24377224763700001, y:0.99923451445199996},{x:0.24406379121899999, y:0.99930449905100005},{x:0.24904163511900002, y:0.99998187031700003},{x:0.25668110709699998, y:0.99911902652899998},{x:0.288576476267, y:0.97076873633300009},{x:0.30129154007699999, y:0.94851756056100001},{x:0.30533724308499999, y:0.94016088484400007},{x:0.31059601909899998, y:0.92839138033700008},{x:0.32875730553400001, y:0.88004150049399998},{x:0.329372080884, y:0.87820053324799996},{x:0.34524661634000003, y:0.82620861316700001},{x:0.351845475149, y:0.80214711787000004},{x:0.35194958510700003, y:0.8017563406899999},{x:0.36241674699500004, y:0.76074558396400005},{x:0.36585147028899995, y:0.74656236921200003},{x:0.36831489684399998, y:0.736175491412},{x:0.38816079891900002, y:0.64628531336399997},{x:0.40857827966799998, y:0.54334764025100002},{x:0.43232723730599998, y:0.41250344424199997},{x:0.44474377991499997, y:0.34025218414700004},{x:0.456000951205, y:0.272946185861},{x:0.468209233153, y:0.19842164120299999},{x:0.47511828206600004, y:0.155700383645},{x:0.49151115018299996, y:0.0533117308543},{x:0.49395610604500001, y:0.037965779127599998},{x:0.49413997608099997, y:0.036811297454800003},{x:0.498953214428, y:0.0065771003053500003},{x:0.54033163910699999, y:-0.25070763092500004},{x:0.54748168257899998, y:-0.29393031939100001},{x:0.56624527103900002, y:-0.40431641968599996},{x:0.57236147176999996, y:-0.43915736141500006},{x:0.59201337384899999, y:-0.54646472259699996},{x:0.60428597257899996, y:-0.60935597236000005},{x:0.60694890447899996, y:-0.622536567594},{x:0.60894686356100003, y:-0.632311546354},{x:0.61380114420300003, y:-0.65564284574999998},{x:0.61414988728600006, y:-0.65729579548899997},{x:0.61587224947700003, y:-0.66541290075000004},{x:0.67996958502200011, y:-0.90474566826900005},{x:0.69542844871099996, y:-0.94178924873199998},{x:0.70990901025900011, y:-0.96844082549499999},{x:0.71522779773, y:-0.97622798960500001},{x:0.72368730721100005, y:-0.98636450574000001},{x:0.72702435288200007, y:-0.9895981426360001},{x:0.72777639697999996, y:-0.99026686126499996},{x:0.74619439575099999, y:-0.99971413808699994},{x:0.75103600088200007, y:-0.999978814025},{x:0.75289941587699993, y:-0.999834064711},{x:0.75691436937500001, y:-0.99905644637899993},{x:0.76072722293999995, y:-0.99772940365500007},{x:0.76203659162000004, y:-0.99714155537499993},{x:0.76450799187399998, y:-0.99584813143099993},{x:0.76842339619599997, y:-0.99330756572899992},{x:0.77024168710899998, y:-0.99192323081200007},{x:0.77714487737600002, y:-0.98549049917000009},{x:0.78727898823599995, y:-0.97269315848999993},{x:0.79947824459099992, y:-0.9520644499680001},{x:0.81894584009499993, y:-0.90762732578999994},{x:0.82360710893900002, y:-0.89494558158299997},{x:0.82920788891200004, y:-0.87869349572400002},{x:0.83903861300500004, y:-0.84754920240600007},{x:0.84066013086700009, y:-0.84209820717700001},{x:0.86843370098499995, y:-0.73567008346399998},{x:0.87227576642399995, y:-0.71910605700800001},{x:0.88574864622000005, y:-0.65777612742799996},{x:0.88726759003999989, y:-0.65055775719599995},{x:0.89696161909899996, y:-0.60312191572600005},{x:0.89718166817, y:-0.60201850282899994},{x:0.90013891383699995, y:-0.58707890116200001},{x:0.90252781439300001, y:-0.57486226097400006},{x:0.91279400520699994, y:-0.52092260114299993},{x:0.94415047390899998, y:-0.34375523822300003},{x:0.94415435212900001, y:-0.34373235552299997},{x:0.94524776323400006, y:-0.33727280147499999},{x:0.94766233645500009, y:-0.322952252233},{x:0.95213597163800001, y:-0.29622569907399998},{x:0.95819410818600004, y:-0.25966391604700001},{x:0.96667265099700006, y:-0.20787491167599997},{x:0.96807248718799999, y:-0.19926368440900002},{x:0.97594639826000007, y:-0.15055854828699999},{x:0.97765203110099996, y:-0.13995545801100001}]
      }]
    },
    options: opts
  });
  // ex5: y=sin(2πx)
  opts.title.text = 'Hình 5. Kết quả dự đoán y=sin(2πx)';
  new Chart('ex5', {
    type: 'line',
    data: {
      datasets: [{
        type: 'bubble',
        label: 'Training Data',
        backgroundColor: 'rgba(54, 162, 235, 0.2)',
        borderColor: 'rgba(54, 162, 235, 1)',
        data: [{x:0.0029817174119300001, y:-0.16607862328799999},{x:0.0040970978991000002, y:0.0098708158100699987},{x:0.060802875311699991, y:0.52682562042400005},{x:0.091589046460899992, y:0.52096596735099998},{x:0.095122973505500003, y:0.55693948823499995},{x:0.10404223774, y:0.71213321193299994},{x:0.10723691203600001, y:0.69094897854600001},{x:0.116083362207, y:0.77520049101199995},{x:0.11751064009000002, y:0.82254265433999996},{x:0.124706389401, y:0.89122412024200004},{x:0.13814181482499999, y:0.8357897651620001},{x:0.14738873420199999, y:1.0286100035100001},{x:0.14967686405200001, y:0.80884968288100001},{x:0.155161234989, y:0.75864165142000006},{x:0.17711196866700002, y:0.86908827240600006},{x:0.18340913565000003, y:0.89268538591399993},{x:0.18887376439100001, y:0.73700428091800008},{x:0.22147238621500001, y:1.04231819518},{x:0.23680135419599999, y:1.1564295722799998},{x:0.24377224763700001, y:1.1200840568100001},{x:0.24406379121899999, y:1.0774348514600001},{x:0.24904163511900002, y:1.2158587435400001},{x:0.25668110709699998, y:1.0224723100199999},{x:0.288576476267, y:0.779368801188},{x:0.30129154007699999, y:0.71708081887399999},{x:0.30533724308499999, y:0.88114014543099994},{x:0.31059601909899998, y:1.08786429857},{x:0.32875730553400001, y:0.63528697671699996},{x:0.329372080884, y:1.0180415090000001},{x:0.34524661634000003, y:0.78319552903300005},{x:0.351845475149, y:0.82464430926999999},{x:0.35194958510700003, y:0.83894558987299994},{x:0.36241674699500004, y:0.74024524606099995},{x:0.36585147028899995, y:0.70185449096200003},{x:0.36831489684399998, y:0.66378369581399999},{x:0.38816079891900002, y:0.61611623223700007},{x:0.40857827966799998, y:0.41789214479300002},{x:0.43232723730599998, y:0.57491556299400004},{x:0.44474377991499997, y:0.29685930976000002},{x:0.456000951205, y:0.32517202423000002},{x:0.468209233153, y:0.40004445012400003},{x:0.47511828206600004, y:-0.123564826118},{x:0.49151115018299996, y:0.183341898078},{x:0.49395610604500001, y:-0.15231906193399999},{x:0.49413997608099997, y:0.0367926647649},{x:0.498953214428, y:0.037741509612500002},{x:0.54033163910699999, y:-0.27144092709500001},{x:0.54748168257899998, y:-0.107793332075},{x:0.56624527103900002, y:-0.36859576177199999},{x:0.57236147176999996, y:-0.327000018896},{x:0.59201337384899999, y:-0.39194779790899997},{x:0.60428597257899996, y:-0.64106955102800001},{x:0.60694890447899996, y:-0.63523859562399998},{x:0.60894686356100003, y:-0.81654814878699999},{x:0.61380114420300003, y:-0.48222487028800004},{x:0.61414988728600006, y:-0.48514128842799997},{x:0.61587224947700003, y:-0.735009179535},{x:0.67996958502200011, y:-0.82229024306199994},{x:0.69542844871099996, y:-1.06955649982},{x:0.70990901025900011, y:-0.81391410155100008},{x:0.71522779773, y:-1.0665997980799999},{x:0.72368730721100005, y:-0.9767992728749999},{x:0.72702435288200007, y:-0.74802596326699999},{x:0.72777639697999996, y:-0.88172644933599997},{x:0.74619439575099999, y:-1.02900145549},{x:0.75103600088200007, y:-0.99835346514599999},{x:0.75289941587699993, y:-1.1934704548399999},{x:0.75691436937500001, y:-1.07840192705},{x:0.76072722293999995, y:-1.0189322191100001},{x:0.76203659162000004, y:-1.09125554863},{x:0.76450799187399998, y:-0.86672403584900004},{x:0.76842339619599997, y:-1.1772112161200001},{x:0.77024168710899998, y:-1.08851040633},{x:0.77714487737600002, y:-0.94303460920900006},{x:0.78727898823599995, y:-0.91232018238500001},{x:0.79947824459099992, y:-0.95772300867100002},{x:0.81894584009499993, y:-0.93580845035400007},{x:0.82360710893900002, y:-0.92101794761400002},{x:0.82920788891200004, y:-1.18043186396},{x:0.83903861300500004, y:-0.93429711519500003},{x:0.84066013086700009, y:-1.0674632531899999},{x:0.86843370098499995, y:-0.95206829674100002},{x:0.87227576642399995, y:-0.545963396416},{x:0.88574864622000005, y:-0.57558252971599999},{x:0.88726759003999989, y:-0.45690446606899998},{x:0.89696161909899996, y:-0.67429568419200003},{x:0.89718166817, y:-0.43462205532100001},{x:0.90013891383699995, y:-0.41296254397600002},{x:0.90252781439300001, y:-0.44431657758900001},{x:0.91279400520699994, y:-0.56750061813999997},{x:0.94415047390899998, y:-0.34870547603500002},{x:0.94415435212900001, y:-0.51550197836800005},{x:0.94524776323400006, y:-0.50698198907300007},{x:0.94766233645500009, y:-0.131712712619},{x:0.95213597163800001, y:-0.24849009312800002},{x:0.95819410818600004, y:-0.306472098586},{x:0.96667265099700006, y:-0.058005464345500003},{x:0.96807248718799999, y:-0.49528400794900002},{x:0.97594639826000007, y:-0.0043999903289099994},{x:0.97765203110099996, y:-0.28296262257999999}]
      }, {
        label: 'Expected Line',
        backgroundColor: 'rgba(0, 128, 0, 1)',
        borderColor: 'rgba(0, 128, 0, 1)',
        fill: false,
        pointRadius: 0,
        data: [{x:0.0029817174119300001, y:0.018733587109499999},{x:0.0040970978991000002, y:0.025739982150699999},{x:0.060802875311699991, y:0.37281021257399999},{x:0.091589046460899992, y:0.54422994883199993},{x:0.095122973505500003, y:0.56272226648199997},{x:0.10404223774, y:0.60814099144599998},{x:0.10723691203600001, y:0.62395172746899996},{x:0.116083362207, y:0.66640248525799994},{x:0.11751064009000002, y:0.67306195904799992},{x:0.124706389401, y:0.70580110115099992},{x:0.13814181482499999, y:0.76301876671299995},{x:0.14738873420199999, y:0.79926470714200004},{x:0.14967686405200001, y:0.80782193378599998},{x:0.155161234989, y:0.82764957917999993},{x:0.17711196866700002, y:0.896952304087},{x:0.18340913565000003, y:0.91373907454799996},{x:0.18887376439100001, y:0.927148251506},{x:0.22147238621500001, y:0.983978707387},{x:0.23680135419599999, y:0.99656331618599991},{x:0.24377224763700001, y:0.99923451445199996},{x:0.24406379121899999, y:0.99930449905100005},{x:0.24904163511900002, y:0.99998187031700003},{x:0.25668110709699998, y:0.99911902652899998},{x:0.288576476267, y:0.97076873633300009},{x:0.30129154007699999, y:0.94851756056100001},{x:0.30533724308499999, y:0.94016088484400007},{x:0.31059601909899998, y:0.92839138033700008},{x:0.32875730553400001, y:0.88004150049399998},{x:0.329372080884, y:0.87820053324799996},{x:0.34524661634000003, y:0.82620861316700001},{x:0.351845475149, y:0.80214711787000004},{x:0.35194958510700003, y:0.8017563406899999},{x:0.36241674699500004, y:0.76074558396400005},{x:0.36585147028899995, y:0.74656236921200003},{x:0.36831489684399998, y:0.736175491412},{x:0.38816079891900002, y:0.64628531336399997},{x:0.40857827966799998, y:0.54334764025100002},{x:0.43232723730599998, y:0.41250344424199997},{x:0.44474377991499997, y:0.34025218414700004},{x:0.456000951205, y:0.272946185861},{x:0.468209233153, y:0.19842164120299999},{x:0.47511828206600004, y:0.155700383645},{x:0.49151115018299996, y:0.0533117308543},{x:0.49395610604500001, y:0.037965779127599998},{x:0.49413997608099997, y:0.036811297454800003},{x:0.498953214428, y:0.0065771003053500003},{x:0.54033163910699999, y:-0.25070763092500004},{x:0.54748168257899998, y:-0.29393031939100001},{x:0.56624527103900002, y:-0.40431641968599996},{x:0.57236147176999996, y:-0.43915736141500006},{x:0.59201337384899999, y:-0.54646472259699996},{x:0.60428597257899996, y:-0.60935597236000005},{x:0.60694890447899996, y:-0.622536567594},{x:0.60894686356100003, y:-0.632311546354},{x:0.61380114420300003, y:-0.65564284574999998},{x:0.61414988728600006, y:-0.65729579548899997},{x:0.61587224947700003, y:-0.66541290075000004},{x:0.67996958502200011, y:-0.90474566826900005},{x:0.69542844871099996, y:-0.94178924873199998},{x:0.70990901025900011, y:-0.96844082549499999},{x:0.71522779773, y:-0.97622798960500001},{x:0.72368730721100005, y:-0.98636450574000001},{x:0.72702435288200007, y:-0.9895981426360001},{x:0.72777639697999996, y:-0.99026686126499996},{x:0.74619439575099999, y:-0.99971413808699994},{x:0.75103600088200007, y:-0.999978814025},{x:0.75289941587699993, y:-0.999834064711},{x:0.75691436937500001, y:-0.99905644637899993},{x:0.76072722293999995, y:-0.99772940365500007},{x:0.76203659162000004, y:-0.99714155537499993},{x:0.76450799187399998, y:-0.99584813143099993},{x:0.76842339619599997, y:-0.99330756572899992},{x:0.77024168710899998, y:-0.99192323081200007},{x:0.77714487737600002, y:-0.98549049917000009},{x:0.78727898823599995, y:-0.97269315848999993},{x:0.79947824459099992, y:-0.9520644499680001},{x:0.81894584009499993, y:-0.90762732578999994},{x:0.82360710893900002, y:-0.89494558158299997},{x:0.82920788891200004, y:-0.87869349572400002},{x:0.83903861300500004, y:-0.84754920240600007},{x:0.84066013086700009, y:-0.84209820717700001},{x:0.86843370098499995, y:-0.73567008346399998},{x:0.87227576642399995, y:-0.71910605700800001},{x:0.88574864622000005, y:-0.65777612742799996},{x:0.88726759003999989, y:-0.65055775719599995},{x:0.89696161909899996, y:-0.60312191572600005},{x:0.89718166817, y:-0.60201850282899994},{x:0.90013891383699995, y:-0.58707890116200001},{x:0.90252781439300001, y:-0.57486226097400006},{x:0.91279400520699994, y:-0.52092260114299993},{x:0.94415047390899998, y:-0.34375523822300003},{x:0.94415435212900001, y:-0.34373235552299997},{x:0.94524776323400006, y:-0.33727280147499999},{x:0.94766233645500009, y:-0.322952252233},{x:0.95213597163800001, y:-0.29622569907399998},{x:0.95819410818600004, y:-0.25966391604700001},{x:0.96667265099700006, y:-0.20787491167599997},{x:0.96807248718799999, y:-0.19926368440900002},{x:0.97594639826000007, y:-0.15055854828699999},{x:0.97765203110099996, y:-0.13995545801100001}]
      }, {
        label: 'Predicted Line',
        backgroundColor: 'rgba(255, 0, 0, 1)',
        borderColor: 'rgba(255, 0, 0, 1)',
        fill: false,
        pointRadius: 1,
        data: [{x:0.0029817174119300001, y:-0.15260110485326794},{x:0.0040970978991000002, y:-0.13940855414310771},{x:0.060802875311699991, y:0.42155684831723772},{x:0.091589046460899992, y:0.64153930089668099},{x:0.095122973505500003, y:0.66324106795781756},{x:0.10404223774, y:0.71487537869702433},{x:0.10723691203600001, y:0.73229017711732936},{x:0.116083362207, y:0.77759505868538348},{x:0.11751064009000002, y:0.78450732062847317},{x:0.124706389401, y:0.81770075365420813},{x:0.13814181482499999, y:0.87242545332851351},{x:0.14738873420199999, y:0.90474892290517173},{x:0.14967686405200001, y:0.91209063288511694},{x:0.155161234989, y:0.92864424762639697},{x:0.17711196866700002, y:0.98057409444452537},{x:0.18340913565000003, y:0.99138077017168547},{x:0.18887376439100001, y:0.99933125753508945},{x:0.22147238621500001, y:1.0204891960010352},{x:0.23680135419599999, y:1.0158014785023615},{x:0.24377224763700001, y:1.0107723125516848},{x:0.24406379121899999, y:1.0105236320877622},{x:0.24904163511900002, y:1.0058101035795248},{x:0.25668110709699998, y:0.99688730141690796},{x:0.288576476267, y:0.93885133031594803},{x:0.30129154007699999, y:0.90705985775576581},{x:0.30533724308499999, y:0.89598682636415417},{x:0.31059601909899998, y:0.88092786576591386},{x:0.32875730553400001, y:0.82340731956879676},{x:0.329372080884, y:0.82131704210826806},{x:0.34524661634000003, y:0.76430833443337942},{x:0.351845475149, y:0.73897153638083157},{x:0.35194958510700003, y:0.7385644547676451},{x:0.36241674699500004, y:0.6965166599969459},{x:0.36585147028899995, y:0.68225130237484066},{x:0.36831489684399998, y:0.67188325966142148},{x:0.38816079891900002, y:0.58445557201844345},{x:0.40857827966799998, y:0.48808913328691794},{x:0.43232723730599998, y:0.36937667808193453},{x:0.44474377991499997, y:0.30507009598093759},{x:0.456000951205, y:0.24573375264267838},{x:0.468209233153, y:0.18051274001589768},{x:0.47511828206600004, y:0.14329419163092894},{x:0.49151115018299996, y:0.054413669911498985},{x:0.49395610604500001, y:0.041115964643740455},{x:0.49413997608099997, y:0.040115706932139616},{x:0.498953214428, y:0.013924836023068821},{x:0.54033163910699999, y:-0.20969456721617519},{x:0.54748168257899998, y:-0.24762310238493868},{x:0.56624527103900002, y:-0.34539442333451298},{x:0.57236147176999996, y:-0.37659831278843825},{x:0.59201337384899999, y:-0.47411628797857475},{x:0.60428597257899996, y:-0.53254542078657785},{x:0.60694890447899996, y:-0.54494049898931607},{x:0.60894686356100003, y:-0.55417039965944781},{x:0.61380114420300003, y:-0.57633830306315836},{x:0.61414988728600006, y:-0.57791655946557086},{x:0.61587224947700003, y:-0.58568244004563574},{x:0.67996958502200011, y:-0.83413794324312107},{x:0.69542844871099996, y:-0.87990132896988982},{x:0.70990901025900011, y:-0.91682403350181119},{x:0.71522779773, y:-0.92885514071958042},{x:0.72368730721100005, y:-0.94621921488451477},{x:0.72702435288200007, y:-0.95245486269795521},{x:0.72777639697999996, y:-0.95381127472629856},{x:0.74619439575099999, y:-0.98123405818986598},{x:0.75103600088200007, y:-0.98653072213036808},{x:0.75289941587699993, y:-0.98834979838594705},{x:0.75691436937500001, y:-0.99184870852927887},{x:0.76072722293999995, y:-0.99463295320457945},{x:0.76203659162000004, y:-0.99546663718437856},{x:0.76450799187399998, y:-0.99686794059896755},{x:0.76842339619599997, y:-0.99862249889748611},{x:0.77024168710899998, y:-0.99924123253969555},{x:0.77714487737600002, y:-1.0004401822577478},{x:0.78727898823599995, y:-0.99882593464167968},{x:0.79947824459099992, y:-0.99137179715484436},{x:0.81894584009499993, y:-0.96640412677871979},{x:0.82360710893900002, y:-0.95794543864975523},{x:0.82920788891200004, y:-0.94647428703462211},{x:0.83903861300500004, y:-0.92281714197002529},{x:0.84066013086700009, y:-0.91847644373365256},{x:0.86843370098499995, y:-0.82409710512407486},{x:0.87227576642399995, y:-0.80796632610398689},{x:0.88574864622000005, y:-0.74524978064885872},{x:0.88726759003999989, y:-0.73756859476839409},{x:0.89696161909899996, y:-0.68556983227695767},{x:0.89718166817, y:-0.68432916207660455},{x:0.90013891383699995, y:-0.66739310371652749},{x:0.90252781439300001, y:-0.65335326687281992},{x:0.91279400520699994, y:-0.58932159350773361},{x:0.94415047390899998, y:-0.35527323921677478},{x:0.94415435212900001, y:-0.35524058807287417},{x:0.94524776323400006, y:-0.34599759774365069},{x:0.94766233645500009, y:-0.32532129467788806},{x:0.95213597163800001, y:-0.28604291370788104},{x:0.95819410818600004, y:-0.23082483101141094},{x:0.96667265099700006, y:-0.14957346894922807},{x:0.96807248718799999, y:-0.13570779694592972},{x:0.97594639826000007, y:-0.055304261449855119},{x:0.97765203110099996, y:-0.037343751751393484}]
      }]
    },
    options: opts
  });
}
</script>
