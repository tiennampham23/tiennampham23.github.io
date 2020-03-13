---
title: "[ICML] Learning Longer-term Dependencies in RNNs with Auxiliary Losses"
date: 2018-08-18
keywords:
- RNN & LSTM
description: "Paper by Trieu H.Trinh, Andrew M.Dai, Minh-Thang Luong, Quoc V.Le. ICML2018. Talk at Tokyo ML Event."
renderMath: true
autoThumbnailImage: true
thumbnailImagePosition: left
thumbnailImage: https://res.cloudinary.com/dominhhai/image/upload/dl/logo.png
metaAlignment: center
---

layout: true
class: center, middle
---
# Learning Longer-term Dependencies in RNNs with Auxiliary Losses

.red[.refer[
Trieu H.Trinh, Andrew M.Dai, Minh-Thang Luong, Quoc V.Le
]]

*19-08-2018*

Do Minh Hai

[<i class="fab fa-github"> @dominhhai</i>](https://github.com/dominhhai)

.footnote[.refer[
[<i class="fab fa-facebook"> Tokyo Paper Reading Fest</i>](https://www.facebook.com/events/176136236447633/)
]]
---
layout: false
class: left

# Outline

- Long-term Dependencies problem in RNNs

- Methodology

  - Auxiliary Losses

  - Procedure

- Exeperiments

  - Model

  - Results

- Analysis
---
# Long-term Dependencies problem
- Long-term dependencies

  - BPTT tend to vanish or explode during training

  - States memory is expensive

- SoTA methods
  - LSTM

  - Gradient Clipping

  - Truncated BPTT

.footnote[.refer[
\# More here: https://dominhhai.github.io/vi/talk/dl-rnn
]]
---
# Methodology
.center[<img width="80%" src="/images/talk-paper-long-term-rnn-1.png" alt="Method Overview">]

### Auxiliary Loss:
- Unsupervised
- Sampling at random anchor points

### BPTT:
  - All gradients are truncated
  - Improves performance and scalability
---
# Methodology
.center[<img width="100%" src="/images/talk-paper-long-term-rnn-2.png" alt="Auxiliary Loss">]

### Auxiliary Loss:
- *r*-LSTM: Reconstruction
  - Reconstructs the past events before anchor point
- *p*-LSTM: Prediction
  - Predict the next events after anchor point

### Anchor Points
- Temporary Memory
- Difference RNNs
---
# Methodology
.center[<img width="100%" src="/images/talk-paper-long-term-rnn-2.png" alt="Auxiliary Loss">]

### Training
- Random anchor points

- Train in 2 phases
  - Pretrain: minimize auxiliary losses only
  - Train: minimize the sum of main objective loss and auxiliary losses
  $$L = L\_\text{supervised} + L\_\text{auxiliary}$$

---
# Methodology
.center[<img width="80%" src="/images/talk-paper-long-term-rnn-2.png" alt="Auxiliary Loss">]

### Sampling
- Add extra hyper-parameters
  - Sampling Frequency $n$
  - Subsequence Length $\\{l_i\\}~~~, i=\overline{1,n}$

### Auxiliary Loss:
$$L\_\text{auxiliary}=\frac{\sum\_{i=1}^n L\_i}{\sum\_{i=1}^n l\_i}$$

  where, the sum of cross-entroy loss $L\_i=\sum\_{t=1}^{l\_i}\text{TokenLost}\_t$
---
# Exeperiments - Datasets
.center[<img width="90%" src="/images/talk-paper-long-term-rnn-3.png" alt="Datasets">]

- Up to 16,000
  - over 20 times longer than any previously use benhmark
---
# Exeperiments - Models

### Main objective model
- Input: embedding size of 128
- 1-layer LSTM with 128 cells
- 2-layers FFN with 256 hidden units
  - Dropout with probability 0.5 on the 2nd-FFN
- Output: softmax prediction

### Auxiliary model
- 2-layers LSTM
  - Bottom layer is initialized with main LSTM states
  - Top layer starts with zero
- 2-layers FFN with 256 hidden units
  - Dropout with probability 0.5 on the 2nd-FFN

#### Hyper-parameters
- $n=1$
- $l_i=600$
---
# Exeperiments - Results
.center[<img width="80%" src="/images/talk-paper-long-term-rnn-4.png" alt="MNIST, pMNIST, CIFAR10">]
---
# Exeperiments - Results
- StandfordDogs

.center[<img width="65%" src="/images/talk-paper-long-term-rnn-5.png" alt="StandfordDogs">]
---
# Exeperiments - Results
- Compare with Transformer

.center[<img width="60%" src="/images/talk-paper-long-term-rnn-6.png" alt="Transformer">]

- DBpedia

  - $l_i=300$

.center[<img width="60%" src="/images/talk-paper-long-term-rnn-7.png" alt="DBpedia">]
---
# Analysis
- Shrinking BPTT length

  - Dataset: CIFAR10

.center[<img width="80%" src="/images/talk-paper-long-term-rnn-8.png" alt="Shrinking BPTT length">]

---
# Analysis
- Multiple Reconstructions with fixed BPTT cost

  - Dataset: CIFAR10

.center[<img width="60%" src="/images/talk-paper-long-term-rnn-9.png" alt="Multiple Reconstructions with fixed BPTT cost">]

- When $l_i$ is constant, we can use batch to utilize data parallelism
---
# Analysis
- Regulaziation and Optimization
  - Dataset: CIFAR10

.center[<img width="56%" src="/images/talk-paper-long-term-rnn-10.png" alt="Regulaziation and Optimization">]

---
# Analysis
- Relative contribution of difference factors to *r*-LSTM's performance

  - Dataset: CIFAR10
  - Turning off part from original full setting

.center[<img width="80%" src="/images/talk-paper-long-term-rnn-11.png" alt="Ablation Study">]

- Jointly training Unsupervised and Supervised loss is most important

- More randomness is better
---
layout: true
class: center, middle
---
# Thank You 😊

Happy Coding!
