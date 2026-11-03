---
layout: page
title: Welford's Algorithm & Sampling Distributions
description: Deriving one-pass variance formulas and analyzing sampling distributions across multiple trajectories.
img: assets/img/12.jpg
importance: 5
category: Statistics
permalink: /projects/homework4/
---

When you first write a script to calculate variance, the instinct is to use the standard textbook formula: calculate the sum of the squares, subtract the square of the sum, and divide by $n$. It works perfectly on a whiteboard. But if you run that in JavaScript over millions of data points, you'll quickly run into *catastrophic cancellation*. Floating-point precision completely falls apart when subtracting two massive numbers.

To fix this, we need a one-pass algorithm that updates the mean and the sum of squared errors (SS) incrementally. 

### Part 1: Deriving Welford’s Recurrence

Let $M_n$ be the sum of squared differences from the mean for $n$ samples: $M_n = \sum_{i=1}^n (x_i - \bar{x}_n)^2$. 
We want to express $M_n$ in terms of $M_{n-1}$ and the new observation $x_n$.

First, let's look at the residual of the new observation relative to the *old* mean. We know Knuth's formula for the mean updates like this:
$$ \bar{x}_n = \bar{x}_{n-1} + \frac{x_n - \bar{x}_{n-1}}{n} $$

By rearranging, we can express the difference between the new mean and the old mean:
$$ \bar{x}_n - \bar{x}_{n-1} = \frac{x_n - \bar{x}_{n-1}}{n} $$

Now, let's expand the definition of $M_n$:
$$ M_n = \sum_{i=1}^n (x_i - \bar{x}_n)^2 = \sum_{i=1}^{n-1} (x_i - \bar{x}_n)^2 + (x_n - \bar{x}_n)^2 $$

We can rewrite the term inside the summation by introducing the old mean $\bar{x}_{n-1}$:
$$ x_i - \bar{x}_n = (x_i - \bar{x}_{n-1}) - (\bar{x}_n - \bar{x}_{n-1}) $$

Plugging this back into the sum and expanding the square:
$$ \sum_{i=1}^{n-1} \left[ (x_i - \bar{x}_{n-1}) - (\bar{x}_n - \bar{x}_{n-1}) \right]^2 $$
$$ = \sum_{i=1}^{n-1} (x_i - \bar{x}_{n-1})^2 - 2(\bar{x}_n - \bar{x}_{n-1})\sum_{i=1}^{n-1}(x_i - \bar{x}_{n-1}) + (n-1)(\bar{x}_n - \bar{x}_{n-1})^2 $$

Notice that $\sum_{i=1}^{n-1}(x_i - \bar{x}_{n-1}) = 0$, because the sum of deviations from the mean is always zero. This kills the middle term. The first term is simply our previous sum of squares, $M_{n-1}$. So we are left with:
$$ M_n = M_{n-1} + (n-1)(\bar{x}_n - \bar{x}_{n-1})^2 + (x_n - \bar{x}_n)^2 $$

Substitute $(\bar{x}_n - \bar{x}_{n-1}) = \frac{x_n - \bar{x}_{n-1}}{n}$ into the equation:
$$ M_n = M_{n-1} + (n-1)\left(\frac{x_n - \bar{x}_{n-1}}{n}\right)^2 + (x_n - \bar{x}_n)^2 $$

Since $(x_n - \bar{x}_n) = \frac{n-1}{n}(x_n - \bar{x}_{n-1})$, you can factor out the terms. After a bit of algebraic cleanup, it collapses beautifully into Welford's recurrence formula:
$$ M_n = M_{n-1} + (x_n - \bar{x}_{n-1})(x_n - \bar{x}_n) $$

In code, this entire mathematical derivation translates to just three core lines of logic inside a loop:

```javascript
// Welford's one-pass algorithm core logic
let delta = x - mean;
mean += delta / n;
M2 += delta * (x - mean); 
// Sample Variance is then simply: M2 / (n - 1)
Part 2: Sampling Mean and Variance in ActionI extended the previous penetration model. Instead of a binary success/fail, each hacker now scores an continuous value between 0 and 10 for each system breached, modeled as a Continuous Uniform Distribution $U(0, 10)$.The theoretical parameters for this distribution are strict:Mean ($\mu$): $(0 + 10) / 2 = 5$Variance ($\sigma^2$): $(10 - 0)^2 / 12 \approx 8.333$The simulation below spawns $m$ hackers, each attempting $n$ systems. It uses the Welford algorithm derived above to compute the sampling mean and sampling variance for each hacker's trajectory. Then, it averages those results across all $m$ hackers.<div class="d-flex flex-column align-items-center mb-3 mt-3">
    <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Number of Hackers (m): <span id="mVal" style="color: #3498db;">200</span></label>
    <input type="range" id="mAttackers" class="custom-range" min="10" max="1000" step="10" value="200" style="width: 65%;" oninput="document.getElementById('mVal').innerText = this.value; runWelfordSim();">
</div>

<div class="d-flex flex-column align-items-center mb-4 mt-3">
    <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Systems per Hacker (n): <span id="nVal" style="color: #e74c3c;">50</span></label>
    <input type="range" id="nSystems" class="custom-range" min="2" max="1000" step="1" value="50" style="width: 65%;" oninput="document.getElementById('nVal').innerText = this.value; runWelfordSim();">
</div>

<hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">

<div class="row w-100 px-4 mb-4 text-center">
    <div class="col-6">
        <h6 class="font-weight-bold" style="color: #2ecc71;">Means Comparison</h6>
        <div style="font-size: 0.9em;">Theoretical $\mu$: <b>5.000</b></div>
        <div style="font-size: 0.9em;">Simulated Average of Means: <b id="simMeanOutput">...</b></div>
    </div>
    <div class="col-6">
        <h6 class="font-weight-bold" style="color: #9b59b6;">Variances Comparison</h6>
        <div style="font-size: 0.9em;">Theoretical $\sigma^2$: <b>8.333</b></div>
        <div style="font-size: 0.9em;">Simulated Avg of Variances: <b id="simVarOutput">...</b></div>
    </div>
</div>

<div class="d-flex flex-column align-items-center w-100">
    <h6 class="font-weight-bold mb-2 text-center">Distribution of the Sampling Means</h6>
    <div class="w-100 px-3 mb-4">
        <canvas id="histMeans" height="100"></canvas>
    </div>

    <h6 class="font-weight-bold mb-2 text-center">Distribution of the Sampling Variances</h6>
    <div class="w-100 px-3 mb-2">
        <canvas id="histVars" height="100"></canvas>
    </div>
</div>
Statistical InterpretationComparing the simulated outputs with the theoretical targets highlights exactly why inferential statistics works.First, regardless of how small $n$ is, the average of the sampling means sits right on top of the theoretical $\mu = 5$. The same happens for the variances: the average of the sampling variances tightly converges to $\sigma^2 \approx 8.333$. This proves practically that the sample mean and the sample variance (using $n-1$, Bessel's correction) are unbiased estimators. If you run the simulation, you'll see that dividing the SS by $n$ instead of $n-1$ consistently underestimates the true variance, which is exactly why the correction exists.The real insight comes from moving the $n$ slider (the number of systems each hacker attempts).
When $n$ is small (e.g., $n=5$), the distributions are wide. A hacker might get "lucky" and score an average of 8, or "unlucky" and score an average of 2.
However, as you increase $n$, the variability of both the sampling means and sampling variances collapses. The histograms narrow into sharp spikes. The standard error of the mean shrinks proportionally to $\sigma / \sqrt{n}$. In a security assessment context, this proves that evaluating a hacker's capability based on a few attempts is statistically unreliable due to high sample variance, but expanding the penetration test across hundreds of systems eliminates the noise and reveals their true expected capability.{% raw %}{% endraw %}
