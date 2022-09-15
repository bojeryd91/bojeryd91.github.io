---
title: 'Superfluous fixed effects'
date: 2020-05-26 00:00:00
excerpt: About the overuse of fixed effects and how they can confuse the interpretation of.
---

Many times have I seen in seminars tables where researchers list all the robustness checks they've done, adding controls, adding fixed effects, mixing them, and interacting them. The coefficient of importance hopefully remains about the same. But then, again, someone will ask for an additional control to be added.

What too often people don't realize is this: what they are asking for is already controlled for by some fixed effects! And the presenter doesn't recognize it as well and says "Ah, I guess I'll have to add that too to the galore of columns I have, (and I'll have to shrink the font even a bit more to make room ...)" Or they have actually already run the regression and jump to that table which shows that the coefficient of interest doesn't change by a single decimal, *and neither* does the $t$-stat/$p$-value.

If that is the case, they probably have added a control variable that is collinear with some fixed effects (FEs) that are already included in the regression. Because people do not pay attention to Stata's innocuous comment "note: 2015.year omitted because of collinearity", they don't notice and gladly add their new results into their new table.

## An example
Let's do a silly example to illustrate my point. Say that we want to test if people growing up with siblings make more money when they are 45. We have a dataset of where person $i$ lived growing up. No one moves anywhere, ever. We also have a dummy variable $X_i$ if they grew up with one or more siblings and then their annual income at age 45 $y_i$.

We believe that the relationship is linear with some additive noise (and let's roll with that being the true model too). And, the place where they grew up has an additive effect on future income. The model is then,

$$
\begin{equation}
y_i = \beta X_i + \eta_{c} + \epsilon_i
\end{equation}
$$

where $\beta$ is the coefficient we're interested in (the effect of growing up with siblings), $\eta_{c}$ is the effect of growing up in place $c$, and $\epsilon_i$ is the noise/error term.

I run this, make some beautiful slides, and start presenting. Then someone in the audience asks "But we also know the average income in each of these places where they grew up, what if that influences future outcomes of the subjects too which biases your results?". Denote this new variable $v_c$ (i.e., the average income in $c$ while $i$ grew up there), the marginal effect of it by $\gamma$, and then the hypothesized new true model is

$$
\begin{equation}
y_i = \beta X_i + \eta_{c} + \gamma v_c + \epsilon_i.
\end{equation}
$$

This might be the true model. It makes sense and looks like it could do something interesting. But, by adding $\gamma v_c$, we will not have made any change what so ever to our estimator of $\beta$ when running a regression. That's because we have added a control variable that is collinear with the fixed effects. And that makes the model not identified.

If you run this is in any statistical program or language it won't be able to invert the matrix when performing OLS. In that case, e.g., Stata will remove one FE to make it invertible. So why do I say it isn't identified? A linear system is invertible if there exists a unique solution. If not, then there can be infinitely many vectors of parameter values that solve the system. Each combination of parameters can explain the data equally well as the true parameters we seek.

I now want to illustrate this point, that the new model (2) has no unique solution:

Assume that we are only considering two cities ($c \in \{1, 2\}$). Take an $\tilde{\eta}_1 \equiv \eta_1 + \Delta$ (a value that is off the true value $\eta_1$ by some value $\Delta$). Then,

$$
\begin{align*}
y_i &= \beta X_i + \tilde{\eta}_1 1(i~grew~up~in~1) + \tilde{\eta}_2 1(i~grew~up~in~2) \\
&\qquad \qquad + \tilde{\gamma} v_1 1(i~grew~up~in~1) + \tilde{\gamma} v_2 1(i~grew~up~in~2) + \epsilon_i \\
%
&= \beta X_i + (\eta_1 + \Delta) 1(i~grew~up~in~1) + \tilde{\eta}_2 1(i~grew~up~in~2) \\
&\qquad \qquad + \tilde{\gamma} v_1 1(i~grew~up~in~1) + \tilde{\gamma} v_2 1(i~grew~up~in~2) + \epsilon_i.
\end{align*}
$$

If I now make some clever choices for $\tilde{\eta}_2$ and $\tilde{\gamma}$, I can recover the model in equation (1). These choices are $\tilde{\eta}_2 \equiv \eta_2 + \frac{\Delta}{v_1}v_2$ and $\tilde{\gamma} = -\frac{\Delta}{v_1}$. Plug these into the last equation and we get

$$
\begin{align*}
y_i &= \beta X_i + (\eta_1 + \Delta) 1(i~grew~up~in~1) + (\eta_2 + \frac{\Delta}{v_1}v_2) 1(i~grew~up~in~2) \\
&\qquad \qquad - \frac{\Delta}{v_1} v_1 1(i~grew~up~in~1) - \frac{\Delta}{v_1} v_2 1(i~grew~up~in~2) + \epsilon_i \\
%
&= \beta X_i + \eta_1 1(i~grew~up~in~1) + \eta_2 1(i~grew~up~in~2) + \epsilon_i \\
&= \beta X_i + \eta_c + \epsilon_i.
\end{align*}
$$

This is model (1), the equation we started with before a seminar participant asked what would happen if we controlled for average income where the subject grew up.

First of all, we see that we can be wrong by an arbitrary value $\Delta$ in our estimate of $\eta_1$ and still get back to the original model. For any $\Delta$, the combination $\left(\tilde{\eta}_1, \tilde{\eta}_2, \tilde{\gamma}\right)$ 

= $\left(\eta_1 + \Delta, \eta + \frac{\Delta}{v_1}v_2, -\frac{\Delta}{v_1}\right)$ will return us to that model. The sum of squared errors will be the same using either estimator (the sum will be $\sum_{i=1}^N \epsilon_i^2$), so they equally well explain the data.

This is not saying that (2) is necessarily wrong, I'm saying that we cannot distinguish between (1) and (2) because we cannot identify the parameters in (2). Because of that, and a remark further down, I think it is better to run with model (1).

But more important are conclusions about $\beta$. Any deviation in the estimator of $\beta$ will increase the sum of squared errors while it is completely unaffected by $\Delta$. So, $\beta$ is still identified in this model and if that's all we're interested in, we don't need to bother with adding controls that are collinear with FEs or if they are identified or not.

You can also include an intercept in either model (1) or (2). Then you will see that the intercept has a different interpretation when using fixed effects. Since some people use the intercept as a benchmark, this matters.

### It generalizes!
It's pretty cool that we don't need to observe all these things that could influence the outcome variable if they are location-specific and we use location-specific FEs (it does matter if people can move around though).

This generalizes to time FEs (if you control for a variable constant within a time period, across the sample, it will be absorbed by time FEs), to individual FEs (say you try to control for parent characteristics in a individual-level panel), etc. If you can write a control variable as $c_t$ (because it doesn't vary across $i$), then it will be collinear with fixed effects $\eta_t$ (unless one is dropped). Fixed effects truly absorb everything that goes on, on average, within the group of observations it covers.

### One last remark
A danger when not paying attention to our fixed effects is when we start making interpretations of the control variables after running an OLS. The output from Stata looks good enough, my important coefficient didn't change, I'm happy. If I ran model (2), controlling for the variable $v_t$ in the two-city scenario, then Stata dropped one of two fixed effects. In this case, say it was $\eta_2$ that was dropped. Then what Stata actually estimated was

$$
\begin{equation}
y_i = \beta X_i + \tilde{\eta}_1 1(i~grew~up~in~1) + \tilde{\gamma} v_1 1(i~grew~up~in~1) + \tilde{\gamma} v_2 1(i~grew~up~in~2) + \epsilon_i.
\end{equation}
$$

I.e., it didn't include $\eta_2$ in the equation to make it possible to estimate $\eta_1$ and $\gamma$. But the true model is as in (2) (we just cannot estimate it).

By matching of coefficients,

$$
\begin{alignat*}{3}
\tilde{\eta}_1 + \tilde{\gamma}v_1 &= \eta_1 + \gamma v_1 &\Rightarrow &&\tilde{\eta}_1 &= \eta_1 + (\gamma - \tilde{\gamma}) v_1,\\
\tilde{\gamma} v_2 &= \eta_2 + \gamma v_2 &\Rightarrow &&\tilde{\gamma} &= \frac{\eta_2}{v_2} + \gamma
\end{alignat*}
$$

And then $\tilde{\eta}_1 = \eta_1 - \frac{\eta_2}{v_2}v_1$. Again, the estimator of $\beta$ is unaffected by whatever collinear control variable is dropped, but the estimators of the controls are not equal to their estimands -- the true values.

In this case, if we would ask "but what is then the marginal effect of $v_c$ on $y_i$?" and we would look at our estimator of $\gamma$ (i.e., $\tilde{\gamma} = \gamma + \frac{\eta_2}{v_2}$) we would be drawing erroneous conclusions. My conclusion: don't interpret control variables when using fixed effects. They will be a mix-up of effects from several sources and reflect covariates that were dropped due to collinearity.

If we also include an intercept term, then also $\eta_1$ will be excluded from the estimations and $\gamma$ will have to explain part of that (as will the intercept). The problem gets worse!
