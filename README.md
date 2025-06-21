# ReLU Networks and Cone Geometry

This repository contains my implementations and explorations related to the theoretical work in [CE23], [CE24], and [Ewa25] on ReLU neural networks and cone geometry. The experiments focus on visualizing how class separation and cone structures can emerge through explicitly constructed networks and gradient-based training.

---

## Papers Referenced
- **CE23**: Geometric Structure of Deep Learning Networks and Construction of Global L2 Minimizers
- **CE24**: Interpretable Global Minima of Deep ReLU Neural Networks on Sequentially Separable Data
- **Ewa25**: Explicit Neural Network Classifiers for Non-Separable Data

---

## Experiment #1: Sequentially Linearly Separable Data (SLS)

### Objective
To implement the CE24 construction on simple SLS data using MNIST digits and explore how well explicitly defined hyperplanes classify clusters.

### Key Features
- Used MNIST digits {0, 1, 2}.
- Defined clusters with Gaussian noise to simulate SLS.
- Visualized hyperplanes and explored both strict and relaxed SLS settings.

### Outcome
- Visualized the hyperplane-based separation.
- Understood and confirmed the SLS theory in CE24.

---

## Experiment #2: Trained ReLU Networks and Cone Visualization

### Objective
To empirically investigate the emergence of cone structures after training a ReLU network, inspired by CE24 and Ewa25.

### Key Tasks
- Trained a CE24-style network (L=Q+1 layers) on MNIST (digits 0, 1, 2).
- Computed cumulative parameters (W_l, b_l).
- Defined truncation maps and extracted cone geometry:
  - Base point: `p = -W_pinv * b`
  - Edge vectors: `v_i = W_pinv * e_i`
- Projected data and cones via PCA and visualized the results.

### Outcome
- Understood how trained ReLU networks can implicitly construct cone-like transformations.
- Developed PCA-based visual tools for interpreting these cones.
---

##Acknowledgements
Special thanks to Professor Chen and Patricia for their guidance and for introducing me to this exciting intersection of geometry and deep learning.
