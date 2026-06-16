---
title: 'Energy DG Semilinear Wave Equation'
date: 2024-01-31
permalink: /posts/2024/01/SemiLinearDG
tags:
  - Energy DG Schemes
  - DG
  - Waves
---

I consider the wave equation as presented by Daniel Appel$\ddot{o}$, Thomas Hagstrom, Qi Wang and Lu Zhang, (Hagstrom et al.) in their paper, "An Energy-based discontinuous Galerkin Method for Semilinear wave equations."

I will show the engergy DG scheme for thier paper applied to $f(u) = sin(u).$ Later we will look at perturbing the equations to simulate turbulence as a basis for understanding reduced order models based on simulated data. The starting equation is: 

$$u_{tt} -{\nu}u_txx = c^{2}\Delta u + sin(u),\quad  x\in\Omega\subset\mathbb{R}^d, t>=0$$

$$c<0, \nu > 0 \to \frac{f(u)}{u} \infty$$

$$u(x,0)=g_1(x), \frac{du}{dt}(x,0)=g_2(x), \quad x\in\Omega\subset\mathbb{R}^d$$

We can use a change of variables to turn it into a system of equations:

$$ u_t-v=0 $$

$$ v_t = \nu v_{xx}+c^2 \Delta u + sin(u) $$

The method requires that $F'(u)=-f(u)$ where in our case, $f(u) = sin(u)$. Checking, if $F(u) = cos(u)$, then $F'(u)=-sin(u) = -f(u).$

Energy Argument
----
Taking the energy as

$$E= \frac{1}{2} \int_{\Omega} v^2 +c^2|\nabla|^2 + 2cos(u) \, dx$$

$$\frac{\partial E}{\partial t} = \int_{\Omega} v\frac{\partial v}{\partial t} + c^2\frac{\partial}{\partial t}|\nabla|^2
 +\frac{\partial}{\partial t}(\frac{2}{2}cos(u)) \,dx$$

Here we will define a fundamental solution for the Laplace operator in any open set. This is known as the greens function. This function will represent a potential due to a unit charge placed at a point $x\in\Omega$ and equal to zero on $\partial\Omega$. such a function $G(x,y)$ satisfies 
$$\begin{cases} 
  \Delta_{y}G(x,y)=-\delta_x & x\in\Omega \\
  G(x,\sigma)=0, & \sigma\in\partial\Omega
  \end{cases} \\
$$
More explicitly, the greens function can be written in the form
$$G(x,y)=\Phi(x-y)-\varphi(x,y)$$
where $\varphi$ solves
$$
\begin{cases}
  \Delta_{y}\varphi=0  & x\in\Omega \\
  \varphi(x,\sigma)=\Phi(x-\sigma)  & \sigma\in\partial\Omega\\
\end{cases}
$$

## The Method of electrostatic images
in this method $\varphi(x,\cdot)$ is considered the potential due to an imaginary charge q and the point $x^*$, the image of x, in the complement of $\Omega$. The charge q and the point $x^*$ have to be chosen so that $\varphi(x,\cdot)$ on $\partial\Omega$ is equal to the potential created by the unit charge in x.

## Example: upper half plane
Let $\mathbb{R}_+^3=\{(x_1,x_2,x_3):x_3>0\}$
Fix $x=(x_1,x_2,x_3)$  and observe that if we choose $x^*=(x_1,x_2,-x_3)$  then, on $y_3=0$ we have $|x^*-y|=|x-y|$
Thus, if $x\in\mathbb{R}_+^3$, $x^*$ belongs to the complement of $\mathbb{R}_+^3$ , the function $\varphi(x,y)=\Phi(x^*-y)=\frac{1}{4\pi|x^*-y|}$ is harmonic in our domain and $\varphi(x,y)=\Phi(x-y)$ on the plane $y_3=0$ 
therefore our greens function is
$$G(x,y)=\frac{1}{4\pi|x-y|}-\frac{1}{4\pi|x^*-y|}$$

## Derivation and usage
Using the basic greens identity number 5 in Salsa, and a delta function representation for a function, $u$
we can find the Poisson's Kernel and formula for both Dirchlet and Neumann boundary conditions.
We have 2 Identities:
1) $\begin{equation}u(x)=\int_{\Omega}\delta(x-y)u(y)dy\end{equation}\tag{1}$
2) $\int_{\Omega}v\Delta u - u\Delta v \space d \mathbf{x} = \int_{\partial \Omega}v\frac{\partial u} {\partial n} - u\frac{\partial v}{\partial n} d\sigma \tag{2}$ 

We also have a fundamental solution $\Phi(\mathbf{x}-\mathbf{y})$, and a function $\psi(\mathbf{x},\mathbf{y})$, as above
where $\mathbf{x}$ and $\mathbf{y}$ are points in our $d-$ dimensional space.

Choose $u=u$ and $v=\Phi$ in $\tag(2)$

3) $\int_{\Omega}\Phi\Delta u - u\Delta \Phi \space d \mathbf{x} = \int_{\partial \Omega}\Phi\frac{\partial u} {\partial n} - u\frac{\partial\Phi}{\partial n} d\sigma \tag{3}$
 but $\Delta\Phi=-\delta(\mathbf{x}-\mathbf{y})$ and $\Delta u = f$ so that
   
4) $u(\mathbf{x}) = \int_{\partial \Omega}\Phi\frac{\partial u} {\partial n} - u\frac{\partial\Phi}{\partial n} d\sigma \tag{3}-\int_{\Omega}\Phi f$  

Choose $u=u$ and $v=\psi$ in $\tag(2)$

5) $\int_{\Omega}\psi\Delta u - u\Delta \psi \space d \mathbf{x} = \int_{\partial \Omega}\psi\frac{\partial u} {\partial n} - u\frac{\partial\psi}{\partial n} d\sigma \tag{3}$  but $\Delta\psi=0$ and $\Delta u = f$ , and switching the signs:
 
6) $0 = -\int_{\partial \Omega}\psi\frac{\partial u} {\partial n} + u\frac{\partial\psi}{\partial n} d\sigma \tag{3}+\int_{\Omega}\psi f$  

If we add 4) and 6) and note that $\frac{\partial G}{\partial n}=\nabla G \cdot \mathbf{n}$ 
We can get Poisson's equation.  
For the Dirchlet case: $u(\mathbf{x})=g(\mathbf{x})$ and $G(\mathbf{x},\mathbf{y})=0$ on the boundary.
$u(\mathbf{x}) = - \int_{\partial\Omega}g(\mathbf{\sigma})\nabla G(\mathbf{x},\mathbf{\sigma})\cdot\mathbf{n}\space d\sigma \tag{3}-\int_{\Omega}G (\mathbf{x},\mathbf{y})f(\mathbf{y}) \space d \mathbf{y}$
Note that $-\nabla G(\mathbf{x},\sigma)$ is called "Poissons' Kernel." In the case that $f=0$, we have just a single term.

For the Neumann case:
Starting from here
$u(\mathbf{x}) = \int_{\partial \Omega}G\frac{\partial u} {\partial n} - u\frac{\partial G}{\partial n} d\sigma \tag{3}-\int_{\Omega}G f$
we need to have the second term on the right hand side vanish. 

$\frac{d\stackrel {\rightharpoonup}{x}}{dt} \ =\ f(\stackrel {\rightharpoonup}{x} ,t,\mu )$
$$ \begin{array}{l}
{\displaystyle \stackrel {\rightharpoonup}{{\displaystyle x}}( t) \ =\ \sum _{k=1}^{n} \ \phi _{k} exp\left( \omega _{k} t\right) b_{k} \ =\ \Phi e^{ \psi{\omega}t} b}\\
x( 0) \ =\ initial\ condition
\end{array}$$