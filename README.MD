# twiss, 2022-2024

<p align="center">
  <img width="150" height="95" src="https://github.com/i-a-morozov/twiss/blob/main/docs/pics/logo.svg">
</p>

Coupled twiss parameters (Wolski twiss matrices) computation for arbitrary even dimension.

# Install & build

```
$ pip install git+https://github.com/i-a-morozov/twiss.git@main
```
or 
```
$ pip install twiss -U
```

# Documentation

[![Run In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/i-a-morozov/twiss/blob/main/docs/source/examples/twiss.ipynb)

[https://i-a-morozov.github.io/twiss/](https://i-a-morozov.github.io/twiss/)

# Twiss

Compute tunes, normalization matrix and twiss matrices.

```python
>>> from math import pi
>>> import torch
>>> from twiss.matrix import  rotation
>>> from twiss.wolski import twiss
>>> m = rotation(2*pi*torch.tensor(0.88, dtype=torch.float64))
>>> t, n, w = twiss(m)
>>> t
tensor([0.8800], dtype=torch.float64)
>>> n
tensor([[1.0000, 0.0000],
        [0.0000, 1.0000]], dtype=torch.float64)
>>> w
tensor([[[1.0000, 0.0000],
        [0.0000, 1.0000]]], dtype=torch.float64)
```

Input matrices can have arbitrary even dimension.

```python
>>> from math import pi
>>> import torch
>>> from twiss.matrix import rotation
>>> from twiss.wolski import twiss
>>> m = rotation(*(2*pi*torch.linspace(0.1, 0.9, 9, dtype=torch.float64)))
>>> t, n, w = twiss(m)
>>> t
tensor([0.1000, 0.2000, 0.3000, 0.4000, 0.5000, 0.6000, 0.7000, 0.8000, 0.9000],
dtype=torch.float64)
```

Can be mapped over a batch of input matrices.

```python
>>> from math import pi
>>> import torch
>>> from twiss.matrix import rotation
>>> from twiss.wolski import twiss
>>> m = torch.func.vmap(rotation)(2*pi*torch.linspace(0.1, 0.9, 9, dtype=torch.float64))
>>> t, n, w = torch.func.vmap(twiss)(m)
>>> t
tensor([[0.1000],
        [0.2000],
        [0.3000],
        [0.4000],
        [0.5000],
        [0.6000],
        [0.7000],
        [0.8000],
        [0.9000]], dtype=torch.float64)
```

Can be used to compute derivatives of observables.

```python
    >>> from math import pi
    >>> import torch
    >>> from twiss.matrix import  rotation
    >>> from twiss.wolski import twiss
    >>> def fn(k):
    ...    m = rotation(2*pi*torch.tensor(0.88, dtype=torch.float64))
    ...    i = torch.ones_like(k)
    ...    o = torch.zeros_like(k)
    ...    m = m @ torch.stack([i, k, o, i]).reshape(m.shape)
    ...    t, *_ = twiss(m)
    ...    return t
    >>> k = torch.tensor(0.0, dtype=torch.float64)
    >>> fn(k)
    tensor([0.8800], dtype=torch.float64)
    >>> torch.func.jacfwd(fn)(k)
    tensor([0.0796], dtype=torch.float64)
```
