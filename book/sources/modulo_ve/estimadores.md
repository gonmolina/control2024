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

# Diseño de estimadores

Hasta ahora aprendimos a obtener la ley de control suponiendo conocido el vector de estados. En general en control no se mido de forma completa todos lo estados del sistema. Por lo tanto será necesario construir un sistema que nos permita __estimar__ los estados de la planta que queremos controlar a partir de lsa variables que conocemos. En general las variables que conocemos son las _salidas_ (mediciones) y las _entradas_ que son las variables que no da nuestro controlador y que serán entradas de los actuadores de la planta.

+++ {"id": "YSG8ObATiDMV"}

## Estimador de orden completo

Con el modelo de la planta, suponiendo que es fiel a la realidad, uno podría pensar en darle las mismas entradas que tiene la planta y de obtener los estados del modelo directamente. Sin embargo esto choca con dos problemas:
* se debe conocer las condiciones iniciales en todo momento, sino el sistema tardará lo que corresponda para  según sus modos para obtener una estimación correcta.
* cualquier error en el modelado pueda hacer divergir las estimaciones respecto al valor de los estados reales sin ningún control de las mismas.

+++ {"id": "cT4SikTwiDMX"}

Por estos problemas, es que se hace lo que mejor hacemos en control, **REALIMENTAR**.

+++ {"id": "_6qBicU_iDMZ"}

La idea es utilizar lo que sabemos de la salida tanto del modelo como de la planta para obtener una estimación de los estados. 

Llamando $\mathbf {\hat x}$ a la estimación de $\mathbf x$ lo que se propone es un estimador que siga la siguiente ecuaciones:

$$\dot{ \hat{ \mathbf{x}}}=\mathbf A\hat{\mathbf{x}}+\mathbf B u+\mathbf L(y-\mathbf C \hat{\mathbf{x}})$$

+++ {"id": "OBIu1_mfiDMa"}

Definimos entonces error de estimación como:

$$\tilde{\mathbf x} = \mathbf x -  \mathbf {\hat {\mathbf x}}$$

Por lo tanto la dinámica del error de estimación resulta:

$$\dot {\tilde {\mathbf{ x}}} = (\mathbf A - \mathbf{LC})\tilde {\mathbf x}$$

+++ {"id": "U9dR4qO3iDMb"}

La ecuación característica del error de estimación entonces e:

$$\det\left[s\mathbf I -(\mathbf{A-LC}\right)]=0$$

+++ {"id": "0hmPx2AOiDMd"}

Esta ecuación no esta diciendo que podemos elegir un $\mathbf L$ de forma tal que la dinámica del error sea la que queramos.

Si se compara con la de la ley de control:

$$\det\left[s\mathbf I -(\mathbf{A-BK}\right)]=0$$

podemos ver ciertas similitudes o semejanzas.

Por ejemplo, si transponemos la ecuación característica del observador resulta:

$$\det\left[s\mathbf I -(\mathbf{A'-C'L'}\right)]=0$$

+++ {"id": "ivR9oWmziDMe"}

De esta ecuación podemos ver que $\mathbf L$ de forma análoga a como obtenemos $\mathbf K$. 

```python
L=(acker(A.T,C.T)).T

```

```{important}
De forma análoga a la ley de control, podemos decir que solo podremos tener una única $L$ que no permite ubicar los polos de la dinámica del error de estimación si y solo si el sistema es observable
```

```{hint}

Y el sistema es observable si y solo si el rango de la matriz de observabilidad $\mathcal O$ es igual al orden $n$ del sistema.

```

```{hint}

A esto se lo conoce como **dualidad** entre los problemas de control y estimación.

```

+++ {"id": "VWKPR_BUiDMg"}

## Estimador de orden reducido

+++ {"id": "T0b-HP_0iDMh"}

La idea de este estimador es reducir el orden del mismo.
Para esto lo que se busca es no estimar las variables que se conocen de antemano, es decir las mediciones.

Escribamos el sistema como:

$$\begin{bmatrix}
\dot {x_a} \\
\dot{\mathbf{ x}_b}\end{bmatrix}=
\begin{bmatrix} A_{aa}&\mathbf A_{ab}\\
\mathbf A_{ba} & \mathbf{A}_{bb}\end{bmatrix}\begin{bmatrix}
x_a \\
\mathbf x_b\end{bmatrix}$$

$$y=[1 \quad\mathbf 0]\begin{bmatrix} x_a \\ \mathbf{x}_b\end{bmatrix}$$

+++ {"id": "3KmAGcHqiDMi"}

La dinámica de las variables no medidas está dada por:

$$\mathbf {\dot x}_b = \mathbf {A}_{bb}\mathbf{x}_b + \underbrace{\mathbf{A}_{ba} x_a + \mathbf B_bu}_{\text{entradas conocidas}}$$

+++ {"id": "UdzkPKv4iDMj"}

La ecuación de salida y de las variables de estado conocidas la podemos expresar como:

$$\underbrace{\dot y - A_{aa}y - B_au}_{\text{mediciones conocidas}} =\mathbf A_{ab}\mathbf{x}_b$$

+++ {"id": "1ZBKu9asiDMk"}

De esta manera podemos hacer un equivalente con el estimador de orden completo.

La primer ecuación la podemos comparar con la ecuación de la dinámica del estimador y la segunda ecuación la podemos comparar con la ecuación de salida, ambas del trabajo realizado para el estimador completo.

Haciendo esto tenemos que:

$$\begin{align*}
\mathbf{x} &\leftarrow \mathbf x_b\\
\mathbf{A} &\leftarrow \mathbf A_{bb}\\
\mathbf{B}u &\leftarrow \mathbf A_{ba}y+\mathbf B_{b}u\\
y &\leftarrow \dot y - A_{aa}y-\mathbf B_{a}u\\
\mathbf{C} &\leftarrow \mathbf A_{ab}\\
\end{align*}$$

+++ {"id": "noiHPiuOiDMl"}

Sustituyendo en las ecuaciones del estimador completo, podemos obtener que las variables estimadas del estimador reducido tienen la siguiente dinámica:

$$\mathbf {\dot {\hat x}}_b = \mathbf {A}_{bb}\mathbf{\hat{x}}_b + \underbrace{\mathbf{A}_{ba} y + \mathbf B_bu}_{\text{entradas}} + \mathbf L ( \underbrace{\dot y -A_{aa}y-B_a u}_{\text{mediciones}}-\mathbf A_{ab}\mathbf{\hat{x}_b})$$

+++ {"id": "QgY1FGggiDMm"}

Entonces definimos el error de estimación como:

$$\mathbf{\tilde{x}_b}=\mathbf x_b-\mathbf{\hat{x}}_b$$

y obtenemos la siguiente dinámica del error:

+++ {"id": "ym7WJGLrGbJ6"}

$$
\mathbf{\dot{\tilde{x}}}_b = \mathbf{\dot{x}_b}-\mathbf{\dot{\hat{x}}_b}
$$

$$
\mathbf{\dot{\tilde{x}}}_b =  \mathbf {A}_{bb}\mathbf{x}_b + \mathbf{A}_{ba} x_a + \mathbf B_bu -  \mathbf {A}_{bb}\mathbf{\hat{x}}_b - \mathbf{A}_{ba} y - \mathbf B_bu - \mathbf L ( \dot y -A_{aa}y-B_a u-\mathbf A_{ab}\mathbf{\hat{x}_b})
$$

$$
\mathbf{\dot{\tilde{x}}}_b =  \mathbf {A}_{bb}\mathbf{\tilde x}_b  - \mathbf L (\underbrace{\dot y - A_{aa}y - B_au}_{\mathbf A_{ab}\mathbf{x}_b}-\mathbf A_{ab}\mathbf{\hat{x}_b}) =  \mathbf {A}_{bb}\mathbf{\tilde x}_b  - \mathbf L \mathbf A_{ab}\mathbf{\tilde x}_b
$$

+++ {"id": "a_pE65tNGie4"}

Finalmente:

+++ {"id": "zS5DhpWrGbJ7"}

$$\mathbf{\dot{\tilde{x}}}_b=(\mathbf A_{bb} -\mathbf L \mathbf A_{ab})\mathbf{\tilde{x}}_b$$

y la ecuación característica resulta:

$$\det\left[s\mathbf I -(\mathbf{A}_{bb}-\mathbf {LA}_{ab}\right)]=0$$

+++ {"id": "MNUU1YgliDMn"}

El problema que tiene el estimador es que depende de la derivada de $y$: uno mide $y$ y a partir de esta variable resuelve $\dot y$, lo cual implica amplificar cualquier ruido presente en la medición:

$$\mathbf {\dot {\hat x}}_b = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})\mathbf{\hat{x}}_b + (\mathbf{A}_{ba}-\mathbf L A_{aa})y + (\mathbf B_b- \mathbf L B_a)u +\mathbf L \dot y $$

Para poder obtener una ecuación del estimador sin la derivada de la medición, lo que se puede hacer es redefinir la variable de estado como:

$$\mathbf x_c \overset{\Delta}{=}\mathbf{\hat{x}_b}-\mathbf Ly$$

Entonces la dinámica del este estimador resulta:

$$\mathbf {\dot {\hat x}}_c = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})\mathbf{\hat{x}}_b + (\mathbf{A}_{ba}-\mathbf L A_{aa})y + (\mathbf B_b- \mathbf L B_a)u $$

+++ {"id": "qCYQwLAZiDMn"}

Para dejarlo todo en función de $\mathbf{x}_c$:

$$\mathbf {\dot {\hat x}}_c = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})(\mathbf{x}_c+\mathbf Ly) + (\mathbf{A}_{ba}-\mathbf L A_{aa})y + (\mathbf B_b- \mathbf L B_a)u $$

+++ {"id": "c8LUE5d7iDMo"}

$$\mathbf {\dot {\hat x}}_c = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})\mathbf{x}_c + (\mathbf{A}_{ba}-\mathbf L A_{aa} + \mathbf {A}_{bb} \mathbf L-\mathbf L\mathbf A_{ab}\mathbf L)y + (\mathbf B_b- \mathbf L B_a)u $$

+++ {"id": "M_pBHg0riDMq"}

## Retomamos el ejemplo de péndulo

Obtener el estimador completo para el péndulo. Ubicar los polos del error del estimador en $10\omega_0$. Es decir 5 veces más rápido que los que ubicamos los polos de la ley de control.

```{code-cell} ipython3
:id: EMtRWJIdiDMr
:tags: [remove-cell]

import control as ctrl
import numpy as np
```

```{code-cell} ipython3
:id: EMtRWJIdiDMr

w0=2
A=[[0,1],[-w0**2,0]]
B=[[0],[1]]
C=[1,0]
D=0
sys=ctrl.ss(A,B,C,D)
```

+++ {"id": "iIbgk2pqGbKG"}

### Cálculo del observador completo

```{code-cell} ipython3
:id: RVnOImHHiDMx

L=(ctrl.acker(sys.A.T,sys.C.T,[10*w0,10*w0])).T
L
```

+++ {"id": "By0jLubjGbKR"}

### Calculo del observador reducido

```{code-cell} ipython3
:id: U0FxBQyWiDM8

n=sys.A.shape[0]
Aaa=sys.A[0:1,0:1]
Aab=sys.A[0,1:]
Aba=sys.A[1:,0:1]
Abb=sys.A[1:,1:]
Ba=sys.B[0:1]
Bb=sys.B[1:]
```

```{code-cell} ipython3
:id: MnME15obGbKY
:outputId: fbd400f6-cb08-4239-bb43-94bf82c39dc8

Lred=(ctrl.acker(Abb.T, Aab.T, [-10*w0])).T # notar que se tata de ubicar un solo polo
Lred
```

## Estimador completo y ley de control

Para obtener un compensador similar a los que teníamos en control clásico, debemos de alguna forma unir el estimador y la ley de control en un solo sistema. Para este sistema compensador, la entrada será la salida de la planta $y(t)$ y la salida será la entrada de la planta $u(t)$. Por ahora consideremos que no tenemos la entrada referencia (o lo que es lo mismo que la referencia es nula).

Recordemos la ecuación del estimador completo:

$$\dot{ \hat{ \mathbf{x}}}(t)=\mathbf A\hat{\mathbf{x}}(t)+\mathbf B u(t)+\mathbf L(y(t)-\mathbf C \hat{\mathbf{x}}(t))$$

y la ecuación de la ley de control es:

$$u(t)=-\mathbf{Kx}(t).$$

Reemplazando la ecuación de la ley de control en la del estimador:

$$\dot{ \hat{ \mathbf{x}}}(t)=\mathbf A\hat{\mathbf{x}}(t)-\mathbf{BKx}(t)+\mathbf L(y(t)-\mathbf C \hat{\mathbf{x}}(t))$$

Y reagrupando tenemos:

$$\dot{ \hat{ \mathbf{x}}}(t)=(\mathbf A-\mathbf{BK-LC)\hat{x}}(t)+\mathbf L y(t)$$

Entonces, tenemos que las ecuaciones de nuestro sistema "compensador" sería:

$$\dot{ \hat{ \mathbf{x}}}(t)= \underbrace{(\mathbf A-\mathbf{BK-LC)}}_{\mathbf{A}_C} \hat{\mathbf x}(t)+\underbrace{\mathbf L }_{\mathbf B_C}y(t)$$

$$u(t)=\underbrace{-\mathbf{K}}_{\mathbf C_C}\mathbf{x}(t) + \underbrace {\mathbf 0}_{\mathbf D} u(t)$$

Estas ecuaciones describen a un sistema controlador donde se estiman los estados con un estimador de orden completo y los polos de la planta se deciden con la ley de control. Más adelante se mostrará como se agregan las referencias a este sistema.

+++

## Estimador de orden reducido y ley de control

Análogamente a lo hecho con el controlador de orden completo, tenmos que 

$$\mathbf {\dot {x}}_c(t) = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})(\mathbf{x}_c(t)+\mathbf Ly(t)) + (\mathbf{A}_{ba}-\mathbf L A_{aa})y(t) + (\mathbf B_b- \mathbf L B_a)u(t).$$

Podemos ahora dividir la $\mathbf K$ entre las partes que son mediciones y las que conocemos, entonces:

$$ \mathbf K = [K_a \quad |\quad \mathbf K_b]$$

La ecuación de salida la podemos del controlador la podemos escribir de la  siguiente manera entonces:

$$u(t) = -K_a y(t) - \mathbf K _b \hat {\mathbf x}_B(t)= -K_a y(t) - \mathbf K _b \mathbf x_C(t) - \mathbf K _b \mathbf L y(t) =\underbrace{-\mathbf K _b}_{\mathbf C_r}\mathbf x_C(t) +\underbrace{(-K_a - \mathbf K _b \mathbf L)}_{\mathbf D_r} y(t)   $$

Reemplanzando en la ecuación del estimador anterior y reagrupando tenemos que:

$$\dot{\mathbf x}_C = \underbrace{((\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})-(\mathbf B_b- \mathbf L B_a)\mathbf K_B )}_{\mathbf A_r}\mathbf x_C +\underbrace{[ (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})\mathbf L +  (\mathbf {A}_{ba}-\mathbf L\mathbf A_{aa}) - (\mathbf B_b- \mathbf L B_a) (K_a + \mathbf K _b \mathbf L) ]}_{\mathbf B_r} y(t)$$

+++

Examinando las ecuaciones anteriores, podemos definir las ecuaciones que describen este compensador en espacio de estados como:

$$ \mathbf A_r = (\mathbf {A}_{bb}-\mathbf L\mathbf A_{ab})-(\mathbf B_b- \mathbf L B_a)\mathbf K_B $$

$$ \mathbf B_r = \mathbf {A}_r \mathbf L +\mathbf A_{ba}-\mathbf L \mathbf A_{aa} - (\mathbf B_b- \mathbf L B_a) K_a$$

$$\mathbf C_r = -\mathbf K_b$$

$$\mathbf D_r = -K_a -\mathbf K_b \mathbf L$$


Por lo tanto las ecuaciones del sistema compensador son:

$$ \dot{\mathbf x}_C = \mathbf A_r \mathbf x_C + \mathbf B_r y(t)$$

$$u(t) = \mathbf C_r \mathbf x_C  + \mathbf D_r y(t)$$

```{code-cell} ipython3

```
