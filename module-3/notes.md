<style>
.katex { font-size: 1.5em !important; }
</style>


# Introduction

**Pattern recognition** is the automated recognition of patterns and regularities in data. One of the approaches to pattern recognition is machine learning.

**Machine learning** is a scientific study of algorithms that perform specific task without explicit instructions.

We divide machine learning into supervised and unsupervised learning.

**Supervised learning** is a task in which we learn a mapping function from input to output based on a set of example input-output pairs.

**Unsupervised learning** discovers patterns in data without a given example output.

In this course we will forcus on supervised learning. The two most prominent problems which we will study are regression and classification.

**Regression** is a task to predict continous value based on input.
**Classfication** on the other hand is a task to assign each input to an element of a discrete set.

# Linear Regression

It's a way of solving a regression task task where we explicitly assume that output can be estimated as simple linear function of the input. The objective is to find that function.

A good example is a task to estimate the price of the house given it's size.

![House prices](assets/houseprices.png)

**Training set** is the set $T = \{(x_i, y_i)\ \in D_x \times D_y \}$ of example input-output pairs  where $x_i$ are the inputs and $y_i$ are outputs.

**Mapping function** is a function $h$ that takes *any* (possibly previously unseen) input $x \in D_x$ and outputs a prediction $h(x) \in D_y$

**Inference** is the step of computing $h(x)$ given x.

We've assumed that $h$ is linear. We thus have a set of two parmas $\theta = \{\alpha,\beta\}$ and can write $h_{\theta}(x) = \alpha x + \beta$.

**Formally the goal of linear regression is to find $\theta$ that minimize $J(h_{\theta}(x), y)$ given some distance function $J$.**

**Cost function** (aka. distance function) the function that measures how much the prediction differs from the ground truth. By far the most popular cost function is **Mean squared error - MSE** defined as $J(\theta) = \frac{1}{2m}\sum_{i=0}^m (h_{\theta}(x) - y)^2$ where $m$ is the number of training samples

We try to define our cost functions to be convex, because then it's easy to find a global minimum (which has to exist if the function is strictly convex).

# Gradient descent

**Learning Algorithm** is a way of finding the parameters that minimize the cost function.

The most widely used learning algorithm is **Gradient Descent**. It's high level strategy is as follows:

- Pick starting params $\theta$, and **learning rate** $\lambda$
- repeat the following steps, until the update is really small:
  * Compute the gradient of the cost function with respect to parameters $g = \nabla_{\theta}(J(\theta))$
  * Update $\theta$ by subtracting $\lambda * g$

The basic idea is that the derivative points into the direction of bigger values of cost function. So if we w<!-- TODO more on BatchGD vs SGD and mention MiniBatchGD -->
ill step in the opposit direction we will (hopefully) arrive at a lower value of cost function. The learning rate determines the size of the step.

> Plotting the cost function is a good way to check the sanity of your hyper-parameters (for example learning rate)

## Learning rate

Picking learning rate is sometimes tricky. **Undershooting** occurs when the learning rate is to small. It can cause the gradient descent to have very small updates and never reach optimum:

![Undershooting graph](assets/undershooting.png)

**Overshooting** is when the learning rate is too big. The gradient descent might never converge:

![Overshooting graph](assets/overshooting.png)



Apart from that gradient descent has some other problems. There are some extensions to tackle them - most notably Batch Gradient Descent. Instead of computing the update for each of the elements in the training set independently it computes the update for whole dataset. Batch gradient descent is computationally more expensive but ensures we generalize well.

<!-- TODO more on BatchGD vs SGD and mention MiniBatchGD -->

# Multiple feature scenario

What happens when the input is a vector not a scalar?
Let's assume $D_x = \reals^N$. For a given $\vec{x_i} \in \reals^N$ we say that it's $j^{th}$ element $x_{ij}$ is the $j^{th}$ feature of $i^{th}$ input sample. 

Now $\theta = \{ \alpha_i \}_{i=0}^{N}$ and thus $h(x_i) = \alpha_{0} + \sum_{j=1}^N \alpha_i * x_{i,j}$.

Surprisingly the gradient descent still works(!). Just pick starting params and keep subtracting $\nabla J * \lambda$ and all the theory still applies.

## Feature normalization

There is one big problem with gradient descent in multidimensional feature space - if the features have different order of magnitude and one wants to use a single learning rate for all the features then there are big differences in the partial derivatives with respect to different features (aka. gradient has elements with different order of magnitude as well). Thus to big $\lambda$ will be bad (we will overshoot some features) and to small $\lambda$ will also be bad (we will undershoot other features).

To tackle the problem we **normalize** the features i.e. make their orders of magnitude the same.

The easiest way to normalize a set is to divide by the element with the biggest absolute value. This approach is not robust against outliers - if we have 10 inputs in range $x_{ij} \in [1,10]$ and one input $x_{i,j}= 10000$ we will compress range $[1,10]$ possibly loosing some information. 

The most common normalization is the **mean normalization**:
  1) center around zero by subtracting mean of all values
  2) divide by variance

Mean normalized features are not in some predefined range but are roughly in the same *scale*. We have to keep the variance and mean to apply same normalisation to input for inference.

# Polynomial regression

What if we our mapping can't be estimated by a linear function? Well here polynomial regression comes into place.

The basic idea is that we can simply make another $N*(k-1)$ features $x_{i,j,n} = (x_{i,j})^n$ for $n = (1 .. k)$. Suprisingly, again nothing changes in terms of algorithm used - just treat them as other features.

We can further complicate this to include some products for instance $x_{i,j}^2 * x_{i,q}$

# Classification - Logistic Regression

We will start with the easiest case - binary classification. It's a task to label the inputs with two classes.

The easiest approach named logistic regression is to perform regression and then do thresholding - if the predicted value is above some level label the input with class $1$ else label with $0$.

To make our life easier with thresholding we use a sigmoid function. Sigmoid function is defined as $g(x) = \frac{1}{1+e^{-x}}$. What we are interested in is that if we will take a linear function $f: \reals \rightarrow \reals$ then we can take mapping function $h(x) = g(f(x))$ and $h$ has two imoportant properties:
  * $h: \reals \rightarrow [0,1]$
  * and $h$ is monotonic (for linear $f$)

We can thus always pick $\frac12$ as a thresholding value. 

We may also interpret $h(x)$ as the probability that the $x$ is labeled with the class $1$.

The **decision boundary** is a set of all points where the probabilities of x belonging to two different classes are equal.

# Non-linear decision boundaries

In many cases there is no good linear solution (decision boundary is not a linear function).

![Red points on edges, green in the center](assets/circleclass.png)

In these cases we have to complicate the solution a bit. We want some kind of a shape for the decision boundary. The approach we use is similar to polynomial regression. We compute another discriminative features. How we do that?

We can just come up with various functions and look whether we can separate the classes using thresholding on those values. For instance we can compute $x_3 = x_1^2 + x_2^2$ and then:

![Graph with the x_3 feature](assets/newfeature.png)

## Cost functions with sigmoid

**MSE** doesn't perform well in logistic regression - it's not a simple convex function:

![Graph of MSE for logistic regression](assets/mselogistic.png)

> This strange outcome is due to the fact that in logistic regression we have the sigmoid function around, which is non-linear (i.e. not a line). [source](https://www.internalpointers.com/post/cost-function-logistic-regression)

It may have some plateaus which extremely slows down gradient descent or even local minima that cause the algorithm to never reach the global minimum.

And so we define a new cost function that plays a lot better with gradient descent in logistic regression: $J(\theta) = \frac{1}{m}\sum_{i=0}^m C(h_{\theta}(x_i),y_i)$ where C is:

$$
C(h_{\theta}(x),y) =
\begin{cases}
  -log(h_{\theta}(x)) & \text{if } y = 1 \\
  -log(1-h_{\theta}(x)) & \text{else} \\
\end{cases}
$$

\(Notice that cases are pain when implementing so we can define the same function as $C(h_{\theta}(x),y) = y*(\text{case 1})+ (1-y)*(\text{case 2})$ \)

Ok, but why this function? Let's look at the graphs for two cases:

![Graph of two cases](assets/costlogistic.png)

It's clear that this function puts a hefty cost for being very sure and wrong at the same time - and that is a desired property in our case.

# Multiclass case

The easiest way to solve multiclass classification is to do *one vs rest* classification. In short lets split the problem to many problems of binary classification.

Let's have labels $\{r,g,b\}$. Let's compute three different models $\forall_{c\in \{r,g,b\}}: c\text{ vs }(\{r,g,b\}\setminus\{c\}$). Then we can just pick the color whose model gave us the highest probability.

One way to see it is that we draw three decision boundaries.

![One vs rest lines](assets/manylines.png)

## Neural networks FF

It's a nice intuition of what fully connected layers do in classification neural network. The first layer computes many possible lines between data points and the following layers combine the results.

# Introduction vol. 2

Let's look differently on linear regression. Let's introduce $\phi: D_X -> D_{X'}$. $\phi$ is a feature extraction function, it takes a data sample $x$ and projects it to a potentially better feature space. For instance for some input $x=[x_1, x_2]$ we may have $\phi(x) = [1, x_1 * x_2^2, x_1^8]$ etc. Now our mapping function $h_\theta: D_X' \rightarrow D_Y$ and generic cost $J(\theta, \phi, (X, Y), C) = \frac{1}{m}\sum_{i=1}^m C(h_\theta(\phi(x_i)), y_i)$, where $(X,Y)$ is the training set and $C$ is cost function of a single output.


## Probabilistics

It's usefull to look at the data in a **generative** way. 
The data (aka. training set) comes from some complex process and is slightly corrupted by noise. We usually assume that the noise is Gaussian. What ML is trying to do is to *model the process*.

We say that with different training sets drawn from the same underlying process we have different **instaces** of the same problem.

For the purpose of this chapter we think of the process as a black box, where we can put our input and get noisy output.

![Instance one of some problem](assets/instance1.png)
![Instance two of the same problem](assets/instance2.png)

## Over(under)fitting
We don't know how complex the process is. Underfitting happens when we try to model a complex process with a simple model.

To make it easier to explain we will assume a 1D feature space. Let's define feature extraction function $\phi_i(x) = [x^j]_{j=0}^{i-1}$. And so $\phi_1(x) = [x^0]$, $\phi_3(x) = [x^0, x^1, x^2]$ etc.

Now let's try to fit some complex model with features extracted by $\phi_0$.

![Underfitting graph](assets/underfit.png)

We can clearly see that this function doesn't provide a good solution. It's a so called *High bias - low variance* solution - the parameters for different instances are alomst the same - hence low variance. The solution is very biased towards some function thus high bias.

Overfitting happens with to complex models. Let's try extracting features with $\phi_{30}$:

![Overfitting graph](assets/overfit.png)

The solution does extremely well on this particular dataset, but given another instance of the same problem it will perform worse than lower-complexity models. We say that this model doesn't **generalize** well.

We call this *low bias - high variance* solution - the parameters for different instances are very different. The solution is very unstable.

### The Bias/Variance trade-off

$error = Bias(f;k)^2 + Var(f;k) + \sigma^2$

where:
 * $Bias$ is a measure of how much relevant data we missed in the model
 * $Var$ is a measure of *sensivity to fluctuations* in data
 * $\sigma^2$ is the unavoidable error due to noise in data

![Bias/Variance trade-off graph](assets/biasvariance.png)

## Regularization

In the regression above we where able to choose to complexity of the model. Generally in ML this doesn't hold.

To tackle the problem of over/under fitting we are going to take a very complex model and control it's *regularity* - the magnitude of weights.

It's based on an observation that in as model is becoming more and more overfitted the magnitude of $\theta$ is getting bigger.

![Magnitude of coefficients](assets/coeffmagnitude.png)

We perform regularization by penalising $\theta^T\theta$ the so-called **regularization term**. Our cost function becomes $J_{reg}(\theta;X;Y;\phi) = J(\theta;X;Y;\phi) + \lambda\theta^T\theta$. The $\lambda$ is called the **regularization coefficient** and it controls the trade-off between fitting the data better and having a smoother function.

Regularization coefficient is a hyper-parameter: we basically try different values and see what works best.

Regularizing with $\theta^T\theta$ (also called **ridge**) is powerfull but it has it's disadvantages - coefficients usually never reach $0$. That's because when we perform gradient descent over $J_{reg}$ as $\theta$ comes closer to $0$ it's derivative is less steep. This is usually not a big problem but sometimes we want $\theta$ to be sparse.

Lasso is another regularization term $\sum_{i=o}^n |\theta_i|$. It has the advantage it's discarding small $\theta_i$ completely. Learning with lasso is much more difficult to implement because it has no derivative. 

Recently we discovered that:

<!-- TODO provide some more info and explanation -->
![Double descent graph](assets/doubledescent.png)

# Notes in progress - warning

> Beyond this point I haven't yet restructured the notes, they may contain errors and they are just a set of loose sentences written during the lecture.

**HELP WANTED**

I want to focus on notes for M4 for now so if somebody could help me structure and rewrite the notes below - it would be great. Feel free to msg me, open an issue or a pull request.

## Parameter estimation - the Bayesian View

<!-- TODO transform random notes to readable text-->

The whole thing till now was to try to estimate values for parameters.

Recap: the bayes rule states $p(Y|X) = \frac{p(X|Y)p(Y)}{p(X)}$, in other words *posterior = likelihood * prior / evidence*

Let's take a 1D dataset $(X,Y)$ of samples drawn from the same distribution. The samples are independent and identically distributed.

Suppose that the underlying distirbution is a Gaussian. We want to estimate the parameters of the Gaussian $(\mu, \sigma^2)$ from the samples we've drawn.

The thing that we are actually trying to do is to find $(\mu, \sigma^2)$ such that we have a highest probability of obtaining the our dataset.

$p(x|\mu,\sigma^2) = \prod_{n=1}^N N(x_n|\mu,\sigma)$

In order to maximize this product we take a log of it. (Maximum Log-likelihood). Log is monotonic, and now we maximize over a sum which is easier:

> One way to view parameter fitting is selecting such a distribution that our dataset of samples is most probable

## Curve fitting

<!-- TODO -->

MSE is a consequence of Gaussian noise in the problem we r trying to model.

Logisitic cost works because of Bernoulli noise.

## MAP

If we have some *prior* of what our weights should be we can leverage that via the Bayesian approach.

Let's assume that we have intuition that the parameters should be around 0

$p(w|\alpha) = N(w|0,\alpha^{-1}I)= (\frac{\alpha}{2\pi})^{(M+1)/2}exp(-\alpha w^t w / 2)$

This leads us to the cost function that is using regularization.

## The last step

Given a training set $(x,y)$, we have probability of having weitgs $p(w|x,y)$. 

<!-- TODO slide bayesian curve fitting with integrating -->

This leads to the high-bias - low-variance balance.



Softmax is weight-combined sigmoid for many outputs.


# Experimental setup

This lecture will be as practical as possible. It's about providing statistical evidence that our models work.

We want to **assess the performance** of a given classifier - see how good is it with previously unseen data. How good is our classifier in **generalisation**.


The classification problem:

We have 2 distributions $A, B$ given by their probability density functions. The task to determine the underlying distribution given a sample is called classification

<!-- TODO These are probably wrong -->
Assumptions:
1) we have a continouus model but a natural number of samples
2) the samples model an underlying distribution


To adapt a classifier to specific problem is to **train** it. 

Bayesian error is a minimal error that we are gonna get. We have an asymptotical error in our problem - whatever we are gonna do we won't do better than bayesian error. Bayesian error doesn't change for different classifiers it does for different feature spaces.

Unbalanced problem is when we have significantly more of class B than of A.

In order to overcome the bayesian errors we move to different features space. Notice that with a fixed number of samples and rising number of features we eventually arrive at huge problems.

Always get a lot of data, otherwise:
  - data may not model the underlying pdf well

We create a model with underlying pdf-s.

is to determine a source distribution 

## Dividing datasets

Training set - the samples that we use to train the models.

**resubstitution error** aka. training error. It's usfull because if the classifiers is bad on seen data then it won't be good on the unseen data. If we fail on many classifiers it means we have a feature space that is not discriminative.

There are two problems with assesing with training error:
 - over fitting
 - <!--TODO from slides-->

 The solution is to divide to test and training set. The classifier is prone to overfitting but the assesment method is not - and that is extremely usefull.


Validation is used to pick (hyper)parmas. Test set is used to asses performance

#### Cross validation && leave-one-out

<!-- TODO from slides -->

### ROC curve

<!-- TODO from slides, change specifity -->

how good we are in:
Sensitivity = detecting all
Specifity = not detecting false samples
Precision = not detecting flase samples


Roc curves has very nice mathematical properties. Shifiting the operetional point of our classifier is the same as balancing it.

The area under the ROC curve is a metric of a classifier

Precision-recall aren't so nice mathematically, but are also usefull for picking an operational point.





## Hypothesis testing

How statistically relevant is our result. It's looking for statistical evidence.

If we have an experiment and some statistical model we want to test how the model is relevant in the real world.

<!-- TODO fill from slides and explain (from youtube) -->
The test of hypothesis consists of:
  - Null hypothesis A condition that we want to reject (otherwise nothing changes)
  - alternative hypothesis: the condition if the null 


# Support Vector Machines

## As a linear classifier

We already know examples of binary classifiers in a multidimensional space. We are going to present mathematical notation for these

$w = (w_0...w_k)$ design decision - **hyperplane** in multidimensional case.

We will be working on 2D to have the visual intuition what is happening.
In 2D the design decision is a line with equation $w^T\phi(x) = 0$.

Our classifier is a condition:

There's no unique solution (hyperplane) dividing the feature space.

SVM is a discriminative linear classifier. It's a way of finding hyperplane. It's based on maximal margin from the support vectors.

Only a few samples from the traingin set are used to compute the hyperplane

We are maximizing the margin between the two classes.

The support vector approach reduces the overfitting. Changes in the support vector samples makes radical changes in the solution

The solution is however invariant to changes in other samples.

SVM-s are thus:
- robust against outliers
- fast - because it computes solution from small number of samples
- linear - Again fast

The approach is only applicable in lineary seperable classes. But life is more complicated - we rarely have separable classes and linear problems.


**soft margin** - The maximal margin condition is relaxed. We are going to use **slack variables** - they control the tolerance to errors.

**kernel trick** - The features space is mapped into a higher dimensional dpace in which the dataset is linearly separable. Recall mapping function $\phi(x)$. (Actually we dont need it we just need to define the scalar product *kernel*)

We not always can find the right $\phi$ and then we fail :/

<!-- TODO fit the headings -->

### Maximal margin solution
## TOlerance to errors
## Nen linearly-separable data sets

## Mathematical development

We have three equations for hyperplanes $h, h^+, h^-$

Classification condition $y_i(w^tx_i+b) \geq 1$

Distance between $h^+ \text{ and } h^-$ is how we measure the margin.

Notice that the distance is dependet on the norm of $\vec{w}$

We will want to minimise $\frac{1}{2} ||w||^2$ subject to: the classification condition.

We've just took the classification problem and brought it down to a well known quadratic optimization problem which we know how to solve.

## Maths

When we use the lagrangian. we want to minimize it. They are minimal where the derivatives are equal zero. And this implies that our $weights$ are just a linear combination of a subset of samples. We arrive at SVM Dual problem which gives us the solution of svm primal.

We define support vectors as those that have the coresponding lagrangian multipliers $\gt 0$.

For the case of non linearly seperable classes we use the kernel trick. The actual trick stems from the fact that svm dual depends on a dot product of the samples. So in fact we can just supply our own **kernel function** $K$ that computes a higher dimension dot product.

We have a whole catalogue of kernel functions:

<!-- TODO fill it -->

Some of them are parametric - we can fix the parameters via cross-validation.

## Slack variables

we are introducint $\varepsilon_i \geq 0$. The dual has another condition that is bounded with the regularization factor. We have to tune it for instance via cross validation.


# References:

<!-- TODO provide all the references from lectures and the source of images -->

https://www.internalpointers.com/post/cost-function-logistic-regression