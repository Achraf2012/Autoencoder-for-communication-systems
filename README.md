# Autoencoder for Communication Systems

This mini-project implements the autoencoder architecture introduced in the paper  
**"An Introduction to Deep Learning for the Physical Layer" (O'Shea & Hoydis, 2017)**.

The objective is to design a **channel autoencoder** for end-to-end communication over an **Additive White Gaussian Noise (AWGN)** channel, and to compare its performance with a classical modulation scheme (QPSK).

---

# 1. Autoencoder Architecture for Communication

An autoencoder is a neural network composed of three main blocks:

- **Encoder**
- **Channel**
- **Decoder**

In classical machine learning, the goal of an autoencoder is to learn a **low-dimensional representation** of an input \( x \), such that it can be reconstructed at the output \( \hat{x} \) with minimal error.

---

## Communication Perspective

In this project, the objective is different.

We consider a set of discrete messages:
\[
s \in \{0, 1, \dots, M-1\}
\]

Each message is encoded using **one-hot encoding** into a vector:
\[
\mathbf{x} \in \{0,1\}^M
\]

The system then operates as follows:

\[
\mathbf{x} \rightarrow \text{Encoder} \rightarrow \mathbf{z} \rightarrow \text{Channel} \rightarrow \mathbf{y} \rightarrow \text{Decoder} \rightarrow \hat{s}
\]

- The **encoder** maps messages to a continuous representation \( \mathbf{z} \in \mathbb{R}^n \)
- The **channel** adds noise (AWGN)
- The **decoder** estimates the transmitted message

---

## Key Idea

Unlike classical autoencoders, the goal here is **not compression**, but:

> Learning a representation that is **robust to channel noise**

The encoder learns a signal representation (constellation points) that minimizes decoding errors after transmission through the noisy channel.

---

## Loss Function

The model is trained using the **categorical cross-entropy loss**:

\[
\mathcal{L} = - \log P(\hat{s} = s \mid \mathbf{y})
\]

This corresponds to **maximizing the likelihood of correct decoding**.

---

# 2. Implementation Details

The architecture follows the structure proposed in the paper:

### Encoder
- Linear layer: \( M \rightarrow M \) + ReLU
- Linear layer: \( M \rightarrow n \)
- Power normalization (unit energy constraint)

### Channel
- AWGN channel:
\[
\mathbf{y} = \mathbf{z} + \mathbf{n}
\]
where \( \mathbf{n} \sim \mathcal{N}(0, \sigma^2) \)

### Decoder
- Linear layer: \( n \rightarrow M \) + ReLU
- Linear layer: \( M \rightarrow M \)
- Output interpreted as logits (softmax applied implicitly via loss)

---

## Training Setup

- Messages are randomly sampled from \( \{0, \dots, M-1\} \)
- One-hot encoding is used as input
- Training is performed using stochastic gradient descent
- A fixed SNR is used during training

---

# 3. Results

## Learned Constellation

The learned encoder representation forms a constellation in \( \mathbb{R}^2 \).

It can be observed that the model **automatically discovers a QPSK-like constellation**, where points are symmetrically distributed to maximize robustness against noise.

This demonstrates that the network is capable of **learning optimal modulation schemes from data**, without explicit prior knowledge.

---

## Performance Evaluation

The performance is evaluated using the **Symbol Error Rate (SER)**:

\[
\text{SER} = P(\hat{s} \neq s)
\]

We compare:

- Learned autoencoder system
- Classical QPSK modulation

Results show that:

- The learned system achieves performance comparable to QPSK
- The SER decreases as SNR increases, as expected
- The learned constellation is well adapted to the AWGN channel

---

# 4. Key Insights

- The autoencoder learns **constellation geometry automatically**
- The system behaves like a **learned communication scheme**
- The latent space corresponds to **signal space**
- The approach connects **deep learning and information theory**

---

# 5. References

- O'Shea, T., & Hoydis, J. (2017).  
  *An Introduction to Deep Learning for the Physical Layer*
