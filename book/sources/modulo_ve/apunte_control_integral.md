---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.1
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Control Integral por Variables de estado

Recordando las clases anteriores notaremos que  para lograr el error nulo en estado estacionario hemos agregado una ganancias $\bar N$, como se puede ver en la figura siguiente:

FIGURA DEL SISTEMA CON NBAR

Podemos notar que esta compensación de error en estado estacionario se encuentra por fuera dela lazo de realimentación. La forma de compensación no es mediante la observación de la entrada sino mediante resultados obtenidos de lo que se conoce del modelo. De esta manera, el $\bar N$ depende del modelo de la planta de y la ubicación de los polos a lazo cerrado.

```{important}
El error de la planta será nulo, en general, si conocemos de manera exacta el modelo en estado estacionario sin perturbaciones.
```

Por lo tanto la solución de agregar un $\bar N$ no resuelve el problema de eliminar o reducir el error de forma robusta.

Para buscar la solución a nuestro problema podemos recurrir a lo que ya sabemos de control clásico: *hacer del sistema un **Sistema Tipo I***

Para esto debemos agregar de alguna forma un polo en cero al sistema. Para esto propondremos un  esquema del siguiente tipo.

FIGURA ESQUEMA DE CONTROL INTEGRAL

Podemos notar en este esquema que hemos agregado integrador (polo en cero) dentro de un lazo de realimentación y agregado la referencia al estilo de como lo hicimos en control clásico.

Las ecuaciones del sistema a controlar que consideramos que tiene una perturbación $w$, las podemos escribir como:

$$
\begin{align}
\mathbf{\dot {x}} &= \mathbf{Ax}+\mathbf{B}u+\mathbf{B_1}w\\
y &= \mathbf{Cx}
\end{align}
$$

Observando el diagrama de bloques propuesto para el control, podemos ver que:

$$\dot{x}_I = e = \mathbf{Cx} - r$$

Por lo tanto:

$$x_I = \int^t e(\tau)d\tau$$

Podemos definir entonces matrices aumentadas del sistema a con el integrador:

+++

$$
\begin{bmatrix}
    \dot{x}_I\\
    \mathbf{\dot x}
\end{bmatrix}
=
\begin{bmatrix}
    0 & \mathbf{C}\\
    \mathbf{0} & \mathbf{A}
\end{bmatrix}
\begin{bmatrix}
    x_I\\
    \mathbf{x}
\end{bmatrix} +
\begin{bmatrix}
    0\\
    \mathbf{B}
\end{bmatrix}u-
\begin{bmatrix}
    1\\
    \mathbf{0}
\end{bmatrix}r+
\begin{bmatrix}
    0\\
    \mathbf{B_1}
\end{bmatrix}w.
$$

+++

Aplicando al sistema la ley de control por realimentación de estados:

$$u=-\begin{bmatrix}
K_I \qquad \mathbf{K_0}
\end{bmatrix}
\begin{bmatrix}
x_I\\
\mathbf x
\end{bmatrix}
$$

o simplemente

$$u=-\mathbf K
\begin{bmatrix}
x_I\\
\mathbf x
\end{bmatrix}
$$

+++

De esta manera podemos aplicar las técnicas ya conocidas de control avanzo a la planta aumentada para obtener el $\mathbf{K}$.
El primer elemento de ese $\mathbf K$ será $K_I$. El resto de los elementos se corresponden a $\mathbf K_0$, que es el que multiplica a los estados de la planta.

+++
