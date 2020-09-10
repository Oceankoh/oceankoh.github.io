---
layout: post
title: Quik Maf Noats
---

## Complex Numbers
### Rectangular form ($a+bi$)

Manual Conversion to Modulus-Argument:
1. Find Modulus, $r$
2. Find Argument, $\theta$ 

	$\alpha=\tan^{-1}|\frac{b}{a}|$ 
	![](https://qph.fs.quoracdn.net/main-qimg-d0e9919f56b55b5e81460a2a537eda53){:width="400"}

3. Express in $r(\cos\theta+i\sin\theta)$ or $re^{i\theta}$

### Modulus-Argument/Polar/Trigonometrical Form ($r(\cos\theta + i\sin\theta)$)

#### Manual Conversion to Rectangular:

Find $\alpha$

* $0<\theta < \frac{\pi}{2}:$  
$\alpha=\theta$
	
* $\frac{\pi}{2}<\theta < \pi$:  
$\alpha=\pi-\theta$
	
* $-\frac{\pi}{2}<\theta<0$:  
$\alpha=-\theta$
	
* $-\frac{\pi}{2}<\theta < -\frac{\pi}{2}$:  
$\alpha=-(\pi-\theta)$

Find $\tan\alpha$, the ratio between imaginary and real parts  
$a=1, b=\tan\alpha$
	
Multiply $a$ and $b$ by the modulus $r$  
Check the sign of $a$ and $b$ to ensure it is in the correct quadrant as denoted by $\theta$

### Euler/Exponential form ($re^{i\theta}$)

#### Manual Conversion to Rectangular:
Same as from Modulus-Argument form

### Finding (complex) roots of polynomial
#### Manual Method
* Quadratic Formula (Degree 2 polynomial)
* Factorize then use Quadratic formula (Degree 3 and above)

#### Using GDC
* cPolyRoots( _polynomial_ )  
`menu`+`3`+`3`+`3`


## Functions
### Theory of Equations
 * Sum of Roots = $-\frac{b}{a}$
 * Product of Roots = $(-1)^n\times\frac{constant}{a}$
 * _Cubic equation_: $\alpha\beta+\beta\gamma+\alpha\gamma=\frac{c}{a}$

##### Useful tricks

* $\alpha^2+\beta^2+\gamma^2=(\alpha+\beta+\gamma)^2-2(\alpha\beta+\beta\gamma+\alpha\gamma)$
* Finding new polynomial: 
	1. Find relation between new roots $y$ and old roots $x$
	2. Substitute $x=f(y)$ into original polynomial equation

### Transformations
* Order of transformations
$y=cf(bx+a)+d$

![](https://useruploads.socratic.org/uQFLN3t1QTWS2uRsX2qF_transformation-rules-graphs.png){:width="750"}

### Common graphs
#### Trigonometry 
![](https://i.pinimg.com/originals/f7/19/e7/f719e7c00ada7f8bd5320d4909fc304d.png){:width="750"}

#### Inverse Trigonometry 
![](https://lh3.googleusercontent.com/proxy/g-Kuxdz-fwmkBpq2FnSzMKyHVRNMmgkjChCDZ7yXr51_kQJSf8PC6AbCEgNVu9mtzlaUb-Qz5iCPdKb2UNyrwO2VjZ1-6ZI8D9OL178lAspkHKU){:width="750"}

#### Reciprocal Function
![](https://lh3.googleusercontent.com/proxy/6y1G-PEhN5MYg0V-gY-RTp7elSLoCLSp3kDmvfWXa6_D56AQrXLUnbZWFX5USp3XstpOnVyvY8UBTYdvOdo1VFZdARz3_uqfu2YxTms2LBQD3TE1r1I){:width="500"}

#### Logarithmic & Exponential
![](https://dr282zn36sxxg.cloudfront.net/datastreams/f-d%3A80525656f38f5a7a15ab4dba8c4a08637c010ea86ee45edc22fcf78c%2BIMAGE_TINY%2BIMAGE_TINY.1){:width="500"}


## Circular Measure
![](https://i1.wp.com/www.greatmathsteachingideas.com/wp-content/uploads/2015/11/Circle-theorems-flash-cards.jpg?fit=1024%2C723){:width="750"}

## Vectors 
### Lines
#### Parametric Equation ($x=a_1+tb_1,\ y=a_2+tb_2,\ z=a_3+tb_3,\ t\in\mathbb{R}$)
To Cartesian: 
* Make $t$ the subject and equate everything

To Vector: 
* Combine all axis to one equation

	$l:\begin{pmatrix}x\\\y\\\z\end{pmatrix}=\begin{pmatrix}a_1\\\a_2\\\a_3\end{pmatrix}+t\begin{pmatrix}b_1\\\b_2\\\b_3\end{pmatrix}$

#### Cartesian Equation ($\frac{x-a_1}{b_1}=\frac{y-a_2}{b_2}=\frac{z-a_3}{b_3}$)

To Parametric:
* Equate to $t$ and make $x/y/z$ the subject

To Vector:
* Convert to parametric then to vector (refer to parametric)

#### Vector Equation ($l:\textbf{r}=\require{physics}\textbf{a}+t\textbf{b},\ t\in\mathbb{R}$)

To Parametric:
* Split the vector into 3 equations for each axis

To Cartesian:
* Split the vector up into each individual axis and make $t$ the subject

#### Parallel, Intersecting or Skew Lines

1. Equate $x,\ y,\ z$ to get 3 linear equations
2. Check if direction vectors are parallel
3. By rref, 
	* Consistent (there is a solution) = intersecting lines
	* Inconsistent (no solution) = skew lines
	* No solution (all zeros)= Coincidental Lines

### Planes
#### Parametric/Vector form ($\Pi:\textbf{r}=\textbf{a}+\lambda \textbf{b}+\mu \textbf{c}\ ,\ \lambda,\mu\in\mathbb{R}$)
To Cartesian:
* Get the normal vector $\textbf n$ by taking the cross product of $\textbf b,\textbf c$
* Find $D$ by taking the dot product of $\textbf a, \textbf n$
* Express in Cartesian form

To Normal Vector:
* Get the normal vector $\textbf n$ by taking the cross product of $\textbf b,\textbf c$
* Find $D$ by taking the dot product of $\textbf a, \textbf n$
* Express in Normal Vector form

#### Cartesian form ($n_1x+n_2y+n_3z=D$)
To Parametric:  
* Find 3 points on the plane
* Form 2 vectors $\textbf b,\textbf c$ using the 3 points
* Using anyone of those 3 points as $\textbf a$ rewrite the equation in parametric form

To Normal Vector:  
* Express $n_1,n_2,n_3$ as column vector $\textbf n$
* Rewrite equation in Normal vector form

#### Normal Vector/Scalar Product form ($\Pi:\textbf{r}\cdot\textbf{n}=a \cdot \textbf n=D$)
To Parametric:
* Find 3 points on the plane
* Form 2 vectors $\textbf b,\textbf c$ using the 3 points
* Using anyone of those 3 points as $\textbf a$ rewrite the equation in parametric form

To Cartesian:
* Rewrite the dot product in to a linear form

#### Intersecting planes

1. Convert equations to Cartesian Form
2. Use rref
* 2 Planes
	* Unique solution = Line of intersection
	* No solution = Parallel Planes
* 3 Planes
	* Unique solution = Point of intersection
	* Infinite solutions (bottom row all zeros)= Line of intersection
	* No solutions (3 zeros = constant)
		* 3 Parallel Planes
		* 2 Parallel Planes and 1 Intersecting 
		* No Parallel Planes with 3 disjoint parallel lines of intersection

### Shapes with vectors

* Area of triangle = 
$\frac{1}{2}|a\times b|$
* Area of parallelogram = 
$|a\times b|$
* Volume of parallelepiped = 
$|a\cdot (b\times c)|$
* Volume of tetrahedron = 
$\frac{1}{6}|a\cdot (b\times c)|$
