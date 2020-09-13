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

## Mathematical Induction
### Free marks statements
* Let $P_n$ be the proposition $\cdots\ ,\ where\ n\in\cdots$
* $n=1,\ LHS=\cdots,\ RHS=\cdots\ $ Thus, since $\ LHS=RHS,\ P_1$ is true
* $n=k$ Assume $P_k$ is true for some $n=k,\ k\in\cdots$
* $n=k+1$ To show $P_{k+1}$ is true, _prove_, thus $P_{k+1}$ is true whenever $P_k$ is true
* Since $P_1$ is true and $P_k$ is true $\implies\ P_{k+1}$ is true, by mathematical induction, $P_n$ is true for $n\in\cdots$ 

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
* $x$ intercepts become vertical asymtotes
* vertical asymtotes become $x$ intercepts
* $y$ intercepts become and horizontal asymtotes become $\frac{1}{y}$
![](https://i.stack.imgur.com/rmdmd.png){:width="500"}

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

Distance of Plane from Origin = $\frac{\|D\|}{\|\textbf n\|}$

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
$\frac{1}{2}|\textbf{a}\times \textbf{b}|$
* Area of parallelogram = 
$|\textbf{a}\times \textbf{b}|$
* Volume of parallelepiped = 
$|\textbf{a}\cdot (\textbf{b}\times \textbf{c})|$
* Volume of tetrahedron = 
$\frac{1}{6}|\textbf{a}\cdot (\textbf{b}\times \textbf{c})|$

## Calculus
### Logarithmic and Exponential Integrals 
* $\int[f(x)]^n\ f^\prime(x)dx=\frac{[f(x)^{n+1}]}{n+1}+C,\ n\neq-1$

* $\int\frac{f^\prime(x)}{f(x)}dx=\ln\lvert f(x)\rvert+C$

* $\int e^{f(x)}f^\prime(x)dx=e^{f(x)}+C$

### Inverse Trigonometric Integrals
* $\int\frac{1}{\sqrt{a^2-(bx)^2}}dx=\frac{1}{b}\sin(\frac{bx}{a})+C$

* $\int\frac{1}{\sqrt{a^2+(bx)^2}}dx=\frac{1}{ab}\tan(\frac{bx}{a})+C$

TODO: implicit differentiation and concavity

## Option: Statistics 
### Expectation Algebra
#### In Normal Distributions
For $X\sim N(\mu,\sigma^2)$,
* Multiplying $X\rightarrow nX$ (E.g. Number on dice roll $\times$ 2)
	* $E(nX)=nE(X)=n\mu$  

	* $Var(nX)=n^2Var(X)=n^2\sigma^2$  

	* Therefore, $nX\sim N(n\mu,n^2\sigma^2)$  
* Multiple independent observations of $X$ (E.g. Sum of 2 dice rolls)
	* $E(X_1+X_2+...+X_n)=nE(X)=n\mu$  

	* $Var(X_1+X_2+...+X_n)=nVar(X)=n\sigma^2$  

	* Therefore, $X_1+X_2+...+X_n\sim N(n\mu,n\sigma^2)$  

For $X\sim N(\mu_1,{\sigma_1}^2)$ and $Y\sim N(\mu_2,{\sigma_2}^2)$,
* Generally, $aX\pm bY\sim N(a\mu_1 \pm b\mu_2, a^2{\sigma_1}^2+b^2{\sigma_2}^2)$

### Normal Distribution $X\sim N(\mu,\sigma^2)$
* $\mu = mean = mode = median$
* Standardize normal form: $Z=\frac{X-\mu}{\sigma}$
	* $z$ point on the standardized normal distribution
	* $x$ point on the original normal distribution

#### Approximate Binomial

Requirements:   
* Large n and p not too small or too large
* $np>5$ and $nq>5$

Approximation: $X\sim N(np,\ npq)$

#### Approximate Poisson 

Requirements:   
* Large $\lambda$, $\lambda > 10$

Approximation: $X\sim N(\lambda,\ \lambda)$

### Geometric Distribution $X\sim Geo(p)$
Models the number of trials required to achieve first success or number of consecutive failures.   
Parameter $p$ is the probability of success. 

Memorylessness property of the geometric distribution means that  
$P((X>a+b)\ |\ (X>a))=P(X>b)$


### Negative Binomial Distribution $X\sim NB(r,p)$
Sum of $r$ independent Geometric Random Vairables.

### Distribution of Sample Mean

Sample mean $\bar X = \frac{X_1+X_2+...+X_n}{n}$
  
Expectation of $\bar X = \frac{E(X_1)+E(X_2)+...+E(X_n)}{n} = \mu$  

Variance of $\bar X = \frac{Var(X_1)+Var(X_2)+...+Var(X_n)}{n^2} = \frac{n\sigma^2}{n^2}=\frac{\sigma^2}{n}$

Therefore, $\bar X \sim N(\mu,\frac{\sigma^2}{n})$

### Confidence Intervals 

Center of confidence Interval: $\bar x$

If $\sigma^2$ is known, use Z-interval 
* $Z_\alpha = invNorm(\frac{1+confidence}{2},\ 0,\ 1)$
* Confidence Interval for $\mu$: $(\bar x - Z_\alpha \times \frac{\sigma}{\sqrt n},\ \bar x + Z_\alpha \times \frac{\sigma}{\sqrt n})$
* Width of confidence interval: $2\times Z_\alpha \times \frac{\sigma}{\sqrt{n}}$  
* Sampling error for $\mu$: $\pm Z_\alpha \times \frac{\sigma}{\sqrt{n}}$
 
If $\sigma^2$ not known, use T-interval and unbaised estimate of $\sigma^2$  

$s^2_{n-1} = \frac{\sum(X_i-\bar X)^2}{n-1} = \frac{\sum (X_i)^2-n\bar X^2}{n-1}=\frac{\sum(X_i)^2-(\frac{\sum X_i}{n})^2}{n-1}=\frac{n}{n-1}\times Sample Variance$ 

* $t_\alpha = invT(\frac{1+confidence}{2},\ n-1)$
* Confidence Interval for $\mu$: $(\bar x - t_\alpha \times \frac{\sigma}{\sqrt n},\ \bar x + t_\alpha \times \frac{\sigma}{\sqrt n})$
* Width of confidence interval: $2\times t_\alpha \times \frac{\sigma}{\sqrt{n}}$  
* Sampling error for $\mu$: $\pm t_\alpha \times \frac{\sigma}{\sqrt{n}}$

### Hypothesis Testing

Null Hypothesis, $H_0$, is a statement assumed to be true (claim), until proven otherwise.  
Alternative Hypothesis, $H_1$, is a statement that there is a difference.

Significance level, $\alpha$, represents the probability of rejecting the null hypothesis when it is true.  

P-value is the probability of getting a value of the test statistic that is at least as extreme as the one representing the sample data, assuming that $H_0$ is true. If probability of getting such an extreme value is high, means that there is not enough evidence to statisically (not by chance) show %H_1% is true.

![](https://i.ytimg.com/vi/DlwOTOydeyk/maxresdefault.jpg){:width="750"}

If $\sigma^2$ is known, use z-test
* $n$ is small, but data assumed to be normally distributed/%n% is large and approximately normal by CLT
* Test statistic: =$\frac{\bar X -\mu}{\sigma/\sqrt(n)}\sim N(0,1)$ 

If $\sigma^2$ is unknown, use t-test
* $n$ is small, assume to be normally distributed/%n% is large and approximately normal by CLT
* Test statistic: $\frac{\bar X -\mu}{s_{n-1}/\sqrt(n)}\sim t(n-1)$


__Freemark statements:__
* $\sigma^2$ is known/unknown, therefore use z-test/t-test
* $H_0: \mu =\ ,\ H_1:\mu=$
* Under $H_0$, test statistic:
* Since p-value $\leq/> \alpha$, reject/do not reject $H_0$  
* _Interpret and contextualise the results to the question_

#### Errors in Hypothesis Testing

| Possible Results | $H_0$ not rejected | $H_0$ rejected  |
| :--------------: | :----------------: | :-------------: |
| $H_0$ is true | Correct Decision (Probability: $1-\alpha$) | Type 1 Error (Probability: $\alpha$) |
| $H_0$ is not true | Type 2 error (Probability $1-\beta$) | Correct Decision (Probability $1-\beta$) |


__Freemark statements__
* $P(Type\ 1\ error) = P(rejecting\ H_0\ when\ H_0\ is\ true)=\alpha$
* $P(Type\ 2\ error) = P(not\ rejecting\ H_0\ when\ H_0\ is\ not\ true)$

### Bivariate Distributions

$E(X) = \sum\ x (P(X=x, Y=y_1)+P(X=x, Y=y_2)+...+P(X=x, Y=y_n))$   
$E(XY) = \sum\ xy P(X=x,Y=y) \neq E(X)(Y)$
* $X$ and $Y$ are not independent variables  

$Cov(X,Y)=E(XY)-E(X)(Y)$
* Covariance postive = positive relationship
* Covariance negative = negative relationship
* Covariance zero = $X,Y$ are independent
	
#### Correlation

Population Product-Moment Correlation Coefficient $= \rho = \frac{Cov(X,Y)}{\sigma_x\sigma_y}$  
Sample Product-Moment Correlation Coefficient $r=\frac{s_{xy}}{s_x s_y}$

Correlation positive = 

### Hypothesis testing for bivariate distributions

$H_0: \rho = 0$  
$H_1: \rho < 0,\ or\ \rho >0\ or\ \rho \neq 0$  

Under $H_0$, test statistic is $R\sqrt{\frac{n-2}{1-R^2}}\sim t(n-2)$

### Regression

Regression line = line of closest fit/best fit line

Coefficient of regression $y$ on $x$: $b=\frac{\sum\limits_{i=1}^{n}(x_i y_i)-n\bar x\bar y}{\sum\limits_{i=1}^{n}(x_i^2)-n\bar x^2}$

Coefficient of regression $x$ on $y$: $d=\frac{\sum\limits_{i=1}^{n}(x_i y_i)-n\bar x\bar y}{\sum\limits_{i=1}^{n}(y_i^2)-n\bar y^2}$

## GDC Skillage
 
|Function|Command Syntax|Example Usage|Output|
|:---|:---|:---|:---|
|Generate Sequence|`seq(formula, variable, lower bound, upper bound)`|seq($n^2$, $n$, 1, 5)|{1,4,9,16,25}|
|Generate Sequence in Table|`menu`+`3`+`1: Generate Sequence`| - | - |
|Jump to bottom of spreadsheet|`Ctrl`+`1`|-|-|
|Jump to top of spreadsheet|`Ctrl`+`7`|-|-|
|Page down|`Ctrl`+`3`|-|-|
|Page up|`Ctrl`+`9`|-|-|
|Find complex roots|`cPolyRoots(polynomial, variable)`|cPolyRoots($z^3-(2i+1)$,$z$)|{-1.01+0.82i, -1.29...}|
|Split table in graph page|`Ctrl`+`T`|-|-|
|Find all intersections|`menu`+`8`+`1`+`3`|-|-|
|Find Magnitude of Vector|`norm(vector)`|norm($\begin{pmatrix}5\\\5\\\5\end{pmatrix}$)|8.66|
|Find point on Normal Distribution with area|`menu`+`6`+`5`+`3: Inverse Normal`|-|-|
|Find point on T Distribution with area|`menu`+`6`+`5`+`6: Inverse t`|-|-|
|Find z-interval|`menu`+`6`+`6`+`1: z Interval`+`Data`/`Stats`|-|-|
|Find t-interval|`menu`+`6`+`6`+`1: z Interval`+`Data`/`Stats`|-|-|
|Hypothesis testing with z-test|`menu`+`6`+`7`+`1: z Test`|-|-|
|Hypothesis testing with t-test|`menu`+`6`+`7`+`2: t Test`|-|-|
|Hypothesis testing of $\rho$|`menu`+`6`+`7`+`A: Linear Reg t Test`|-|-|
|Find regression equation|`menu`+`6`+`7`+`A: Linear Reg t Test` or <br>`menu`+`4`+`6`+`2` in Data and Statistics page|-|-|
|Select All|`Ctrl`+`A`|-|-|
|Copy|`Ctrl`+`C`|-|-|
|Paste|`Ctrl`+`V`|-|-|
|Cut|`Ctrl`+`X`|-|-|
|Undo|`Ctrl`+`Z`|-|-|
|Redo|`Ctrl`+`Y`|-|-|
|Enter new graph on graph page|`tab`|-|-|
