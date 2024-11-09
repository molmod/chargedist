[![Creative Commons License](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

All files in this repository are licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0) license](http://creativecommons.org/licenses/by-nc-sa/4.0/).

This tutorial is written by Toon Verstraelen for students of the course
["Elektriciteit, Magnetisme en Sensoren" (I002429)](https://studiekiezer.ugent.be/2024/studiefiche/en/I002429)
of the [B.Sc. Bioscience Engineering](https://studiekiezer.ugent.be/2025/bachelor-of-science-in-bioscience-engineering/programma)
at [Ghent University](https://www.ugent.be/).


# Computational Tutorial Electromagnetism: charge distributions in copper nanostructures

## Overview

This repository contains two Jupyter notebooks:

- `1_slice_of_numpy.ipynb`: a crash course on NumPy for absolute beginners.
  It does not attempt to give a complete introduction to NumPy.
  Instead, it covers only the basics needed to work on the second notebook.
- `2_charge_nano.ipynb`: the assignment to be solved.


## How to get started.

- You can open the notebooks with Google Colab using the following links:

    - [1_slice_of_numpy.ipynb on Google Colab](http://colab.research.google.com/github/molmod/chargedist/blob/main/1_slice_of_numpy.ipynb)
    - [2_charge_nano.ipynb on Google Colab](http://colab.research.google.com/github/molmod/chargedist/blob/main/2_charge_nano.ipynb)

    The second notebook needs additional data files.
    One of the code cells in the notebook can be used to download them into your runtime on Colab.

- You can also work with the notebooks locally by downloading [this repository as a ZIP file](https://github.com/molmod/chargedist/archive/refs/heads/main.zip) and unzipping it on your computer.
  You can then open the notebooks using Jupyter Lab or VSCode.

## Theory recap

The charge model in this tutorial is a flavor of
[electronegativity equalization](https://doi.org/10.1021/ja00275a013)
or [charge equilibration](https://doi.org/10.1021/j100161a070).
Although this model was originally proposed to calculate charge distributions in organic molecules, it is better suited to describe metallic systems. The energy as a function of the charges has the form:

$$
U = \sum_{i=1}^N B_i q_i + \frac{1}{2} \sum_{i=1}^N \sum_{j=1}^N q_i A_{ij} q_j
$$

with

$$\begin{aligned}
    B_i &= \chi
    \\
    A_{ij} &= \begin{cases}
        \eta &
        \text{if $i=j$}
        \\
        \frac{1}{4\pi\epsilon_0 \left\Vert \vec{R}_i - \vec{R}_j \right\Vert} &
        \text{if $i \neq j$}
    \end{cases}
\end{aligned}$$

where $q_i$ are atomic partial charges and $\vec{R}_i$ are the particle positions.
The electronegativity $\chi$ and hardness $\eta$ are constant model parameters.
In this tutorial, we'll assume elemental systems, so all atoms have the same parameters.

The off-diagonal second-order terms represent the Coulumb interaction between point charges.
To obtain a positive definite potential energy expression
and to make the model a bit more realistic, we will use a damped electrostatic interaction instead:

$$
A_{ij} = \frac{1 - \exp(-r_{ij}/\sigma)}{4\pi \epsilon_0\, r_{ij}}
$$

It can be shown that the energy is positive definite for any configuration of the atoms when $\eta > 1 / 4 \pi \epsilon_0 \sigma$.

The minimizer of the energy with a fixed total charge corresponds to the stationary point of the following Lagrangian:

$$
L = U + \lambda \left(\sum_{i=1}^N q_i - q_\text{tot}\right)
$$

The ground-state charge distribution is therefore found by solving the following system of linear equations:

$$
\begin{bmatrix}
    A_{11} & A_{12} & \cdots & A_{1N} & 1 \\
    A_{21} & A_{22} & \cdots & A_{2N} & 1 \\
    \vdots & \vdots & \ddots & \vdots &  \vdots \\
    A_{N1} & A_{N2} & \cdots & A_{NN} & 1 \\
    1 & 1 & \cdots & 1 & 0
\end{bmatrix} \begin{bmatrix}
    q_1 \\
    q_2 \\
    \vdots \\
    q_N \\
    \lambda
\end{bmatrix} = \begin{bmatrix}
    \chi \\
    \chi \\
    \vdots \\
    \chi \\
    q_\text{tot}
\end{bmatrix}
$$
