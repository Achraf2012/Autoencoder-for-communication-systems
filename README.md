# Autoencoder for Communication Systems

This mini-project implements the autoencoder architecture introduced in the paper  
**"An Introduction to Deep Learning for the Physical Layer" (O'Shea & Hoydis, 2017)**.

The objective is to design a **channel autoencoder** for end-to-end communication over an AWGN channel, and to compare its performance with a classical modulation scheme (QPSK).

---

# 1. Autoencoder Architecture for Communication

An autoencoder is a neural network composed of three main blocks:
**Encoder** , **Channel** ,**Decoder**

In classical machine learning, the goal of an autoencoder is to learn a **low-dimensional representation** of an input $x$, such that it can be reconstructed at the output x̂ with minimal error.

---

## Communication Perspective

In this project, the objective is different. We consider a set of discrete messages: s ∈ {0, 1, ..., M-1}

Each message is encoded using **one-hot encoding** into a vector x ∈ {0,1}^M

The system then operates as follows:

x → Encoder → z → Channel → y → Decoder → ŝ

- The **encoder** maps messages to a continuous representation z ∈ ℝⁿ
- The **channel** adds an awgn noise
- The **decoder** estimates the transmitted message

---

## Key Idea

Unlike classical autoencoders, the goal here is **not compression**, but learning a representation that is **robust to channel noise**

The encoder learns a signal representation that minimizes decoding errors after transmission through the noisy channel.

---

## Loss Function

The model is trained using the **categorical cross-entropy loss**: L = -log P(ŝ = s | y)

This corresponds to **maximizing the likelihood of correct decoding**.

---

# 2. Implementation Details

The architecture follows the structure proposed in the paper:

### Encoder
- Linear layer: \( M → M \) + ReLU
- Linear layer: \( M → n \)
- Power normalization (unit energy constraint)

### Channel
- AWGN channel: y = z + n , where n ~ N(0, σ²)

### Decoder
- Linear layer: \( n → M \) + ReLU
- Linear layer: \( M → M \)
- Output interpreted as logits

---

## Training Setup

- Messages are randomly sampled from {0, ..., M-1}
- One-hot encoding is used as input
- Training is performed using stochastic gradient descent
- A fixed SNR (SNR = 10dB) is used during training

---

# 3. Results

## Learned Constellation

The learned encoder representation forms a constellation in ℝ².

It can be observed that the model **automatically discovers a QPSK like constellation**, where points are symmetrically distributed to maximize robustness against noise.

This demonstrates that the network is capable of **learning optimal modulation schemes from data**, without explicit prior knowledge.

---

## Performance Evaluation

The performance is evaluated using the **Symbol Error Rate (SER)**:

SER = P(ŝ ≠ s)

We compare:

- Learned autoencoder system
- Classical QPSK modulation

Results show that:

- The learned system achieves performance comparable to QPSK
- The learned constellation is well adapted to the AWGN channel

---

# 5. References

- O'Shea, T., & Hoydis, J. (2017).  
  *An Introduction to Deep Learning for the Physical Layer*
