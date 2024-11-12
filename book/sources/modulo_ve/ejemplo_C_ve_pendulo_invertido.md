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


# Diseño del sistema de control del pédulo invertido

```{code-cell} ipython3
import control as ctrl
import numpy as np
import matplotlib.pyplot as plt
```

+++

Seguimos con el ejemplo de teoría, pero con un pequeño cambio:

```{code-cell} ipython3
w0=2
A=[[0,1],[-w0**2,0]]
B=[[0],[1]]
C=[1,0]
D=0

pendulo=ctrl.ss(A,B,C,D)
pendulo
```

+++

## Obtención de la ley de control

```{code-cell} ipython3
K=ctrl.acker(pendulo.A,pendulo.B,[-2*w0,-2*w0])
K
```

+++

## Diseño del estimador completo

```{code-cell} ipython3
L=ctrl.acker(pendulo.A.T,pendulo.C.T,[-4*w0,-4*w0]).T
```

```{code-cell} ipython3
L
```

```{code-cell} ipython3
np.linalg.eigvals(pendulo.A-pendulo.B@K)
```

```{code-cell} ipython3
np.linalg.eigvals(pendulo.A-L@pendulo.C)
```

+++

## Definimos el sistema compensador ( entrada $y$, salida $u$)

```{code-cell} ipython3
s=pendulo
Acomp=s.A-s.B@K-L@s.C
Bcomp=L
Ccomp=-K
comp=ctrl.ss(Acomp,Bcomp,Ccomp,0)
comp
```

```{code-cell} ipython3
np.linalg.eigvals(comp.A)
```

+++

Vemos que los polos del compensador son estables!

```{code-cell} ipython3
 comp.zeros()
```

+++

Verificamos que los polos a lazo cerrado del sistema compensado son los adecuados:

```{code-cell} ipython3
sys_comp = ctrl.feedback(-s*comp)
sys_comp.poles()
```

+++

Ahora verificamos que el sistema a lazo cerrado tiene efectivamente los polos donde se pretendía. Igualmente será necesario rediseñar para lograr un controlador que sea correcto.

+++

## Sistema completo

```{code-cell} ipython3
sys_completo =  ctrl.connect(ctrl.append(s,comp),[[1,2],[2,1]],[1],[1,2])
t,y = ctrl.initial_response(sys_completo, X0=[1,0,0,0], T=np.linspace(0,3,1001))
```

```{code-cell} ipython3
fig, ax = plt.subplots(2,1,figsize=(12,8))
ax[0].plot(t,y[0,:],label="sys completo")
ax[0].set_title('Respuesta al escalón en la referencia')
ax[0].set_xlabel("Tiempo")
ax[0].set_ylabel("Salida")
ax[0].grid()
ax[1].plot(t,y[1,:],label="sys completo")
ax[1].set_title('Respuesta al escalón en la referencia')
ax[1].set_xlabel("Tiempo")
ax[1].set_ylabel("Salida")
ax[1].grid()
fig.tight_layout()
```

```{code-cell} ipython3
np.linalg.eigvals(sys_completo.A)
```

+++

## Ubicación óptima de los autovalores del estimador

```{code-cell} ipython3
B1=B

def conjugate_tf(G):
    num = ctrl.tf(G).num[0][0]
    den = ctrl.tf(G).den[0][0]
    nume = [num[i]*((-1)**(len(num)%2+1-i)) for i in range(0, len(num))]
    dene = [den[i]*((-1)**(len(den)%2+1-i)) for i in range(0, len(den))]
    return ctrl.tf(nume,dene)
```

```{code-cell} ipython3
aux1 = ctrl.ss(A,B1,C,D)
aux2 = conjugate_tf(aux1)
G_srle = ctrl.tf(aux1)*aux2
ctrl.rlocus(G_srle, xlim=[-8,8], ylim=[-10,10]); #aumento los límites para ver la zona donde quiero ubicar el polo
plt.gcf().set_size_inches(8,6)
```

```{code-cell} ipython3
k=12000 # lo busco haciendo clcik sobre las lineas del root locus simétrico
```

```{code-cell} ipython3
pz= ctrl.root_locus_map(G_srle, gains=[12000])
pz.poles
```

```{code-cell} ipython3
r=pz.poles
rsel = r[np.real(r)<0]
rsel
```

```{code-cell} ipython3
L = ctrl.place(pendulo.A.T, pendulo.C.T, rsel).T
L
```

```{code-cell} ipython3
comp_srl = ctrl.ss(s.A-s.B@K-L@s.C, L, -K, 0)
comp_srl.poles()
```

```{code-cell} ipython3
sys_completo_srl =  ctrl.connect(ctrl.append(s,comp_srl),[[1,2],[2,1]],[1],[1,2])
t_srl, y_srl = ctrl.initial_response(sys_completo_srl, X0=[1,0,0,0], T=np.linspace(0,3,1001))
```

```{code-cell} ipython3
fig, ax = plt.subplots(2,1,figsize=(12,8))
ax[0].plot(t,y[0,:],label="sys completo 2do orden dominante")
ax[0].plot(t_srl,y_srl[0,:],label="sys completo SRL")
ax[0].set_title('Respuesta al escalón en la referencia')
ax[0].set_xlabel("Tiempo")
ax[0].set_ylabel("Salida")
ax[0].grid()
ax[1].plot(t,y[1,:],label="sys completo 2do orden dominante")
ax[1].plot(t_srl,y_srl[1,:],label="sys completo SRL")
ax[1].set_title('Respuesta al escalón en la referencia')
ax[1].set_xlabel("Tiempo")
ax[1].set_ylabel("Salida")
ax[1].grid()
fig.tight_layout()
```
