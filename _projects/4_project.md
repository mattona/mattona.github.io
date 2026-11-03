---
layout: page
title: Streaming Variance & Hacker Simulation
description: Deriving Welford's algorithm and simulating penetration scores to analyze sampling distributions.
img: assets/img/12.jpg
importance: 4
category: Statistics
permalink: /projects/homework4/
---

For this assignment, the goal was to tackle numerical instability in variance calculations using Welford's algorithm, and then apply it to a large-scale stochastic simulation. Computing variance naively (by accumulating $\sum x$ and $\sum x^2$) often leads to catastrophic cancellation due to floating-point precision issues. Welford's method avoids this by updating the mean and sum of squares incrementally. 

### 1. Welford’s Recurrence Formulas

To compute the variance on the fly, we need to maintain the sum of squared differences from the current mean, denoted as $M_{2,n} = \sum_{i=1}^n (x_i - \bar{x}_n)^2$. 

Following Knuth's approach for the mean ($\bar{x}_n = \bar{x}_{n-1} + \frac{x_n - \bar{x}_{n-1}}{n}$), we can express $M_{2,n}$ in terms of $M_{2,n-1}$:

$$
\begin{align*}
M_{2,n} &= \sum_{i=1}^n (x_i - \bar{x}_n)^2 \\
&= \sum_{i=1}^{n-1} (x_i - \bar{x}_n)^2 + (x_n - \bar{x}_n)^2 
\end{align*}
$$

Substituting $\bar{x}_n = \bar{x}_{n-1} + \frac{x_n - \bar{x}_{n-1}}{n}$, we rewrite the inner term:
$$ x_i - \bar{x}_n = (x_i - \bar{x}_{n-1}) - \frac{x_n - \bar{x}_{n-1}}{n} $$

Expanding the sum and simplifying (relying on the fact that $\sum_{i=1}^{n-1} (x_i - \bar{x}_{n-1}) = 0$), the cross terms cancel out elegantly, leaving us with:

$$ M_{2,n} = M_{2,n-1} + (x_n - \bar{x}_{n-1})(x_n - \bar{x}_n) $$

Once we have $M_{2,n}$, the sample variance is simply $s_n^2 = \frac{M_{2,n}}{n-1}$. This leads to a beautifully compact, three-line core logic in code:

```javascript
// Welford's Online Algorithm (Core Logic)
let delta = x - mean;
mean += delta / n;
M2 += delta * (x - mean);
2. Sampling Mean and Variance SimulationFor the simulation, I set up a scenario where $m$ hackers attack $n$ systems. Each penetration attempt yields a score from $0$ to $10$.
I chose a Discrete Uniform Distribution $U\{0, 10\}$ for the scores.Theoretical Mean ($\mu$): $\frac{a+b}{2} = \frac{0+10}{2} = 5$Theoretical Variance ($\sigma^2$): $\frac{(b-a+1)^2 - 1}{12} = \frac{11^2 - 1}{12} = 10$For each hacker, the engine generates $n$ scores and uses Welford's algorithm to track their individual sampling mean ($\hat{\mu}$) and sampling variance ($\hat{\sigma}^2$). We then average these statistics across all $m$ hackers and compare them to the theoretical $\mu$ and $\sigma^2$.<div class="d-flex flex-column align-items-center mb-3">
    <label class="font-weight-bold mb-1" style="width: 85%; text-align: left;">Number of Hackers (m): <span id="mVal" style="color: #3498db;">1000</span></label>
    <input type="range" id="mAttackers" class="custom-range" min="100" max="5000" step="100" value="1000" style="width: 85%;" oninput="document.getElementById('mVal').innerText = this.value; runSimulation();">
</div>

<div class="d-flex flex-column align-items-center mb-4 mt-3">
    <label class="font-weight-bold mb-1" style="width: 85%; text-align: left;">Number of Systems Attempted (n): <span id="nVal" style="color: #e74c3c;">50</span></label>
    <input type="range" id="nSystems" class="custom-range" min="2" max="1000" step="10" value="50" style="width: 85%;" oninput="document.getElementById('nVal').innerText = this.value; runSimulation();">
</div>

<hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">

<!-- Stats Row -->
<div class="row w-100 px-4 text-center mb-4">
    <div class="col-6">
        <h6 class="font-weight-bold">Averages of Sample Means</h6>
        <p class="mb-0">Theoretical $\mu$: <strong>5.000</strong></p>
        <p class="mb-0" style="color: #2ecc71;">Empirical $\bar{\mu}$: <strong id="empMean">--</strong></p>
    </div>
    <div class="col-6">
        <h6 class="font-weight-bold">Averages of Sample Variances</h6>
        <p class="mb-0">Theoretical $\sigma^2$: <strong>10.000</strong></p>
        <p class="mb-0" style="color: #9b59b6;">Empirical $\bar{\sigma}^2$: <strong id="empVar">--</strong></p>
    </div>
</div>

<!-- Charts Section -->
<div class="d-flex flex-column align-items-center w-100">
    <h6 class="font-weight-bold mb-2 text-center">Distribution of Sample Means ($\hat{\mu}$)</h6>
    <div class="w-100 px-3 mb-5">
        <canvas id="histMeans" height="100"></canvas>
    </div>

    <h6 class="font-weight-bold mb-2 text-center">Distribution of Sample Variances ($\hat{\sigma}^2$)</h6>
    <div class="w-100 px-3 mb-2">
        <canvas id="histVars" height="100"></canvas>
    </div>
</div>
Conclusions on Inferential StatisticsBy playing with the slider for $n$, a few statistical phenomena become immediately obvious, serving as the foundation of inferential statistics:Unbiased Estimators: Regardless of whether $n$ is 5 or 1000, the average of the sample means stays tightly clustered around the theoretical $\mu$ (5.0), and the average of the sample variances stays near $\sigma^2$ (10.0). This visually proves that the sample mean and the sample variance (divided by $n-1$) are unbiased estimators of the population parameters.The Weak Law of Large Numbers (WLLN): As you increase $n$, the variance of the sample means shrinks drastically. Algebraically, the variance of the sampling distribution of the mean is $\frac{\sigma^2}{n}$. A larger $n$ crushes the variability, causing the histogram of sample means to collapse into a highly precise spike exactly at $5$.Consistency: Because the variability of both estimates drops as $n \to \infty$, our estimates converge in probability to the true parameters. This is why in real-world cybersecurity auditing or statistical polling, taking a larger sample size $n$ gives us a exponentially higher confidence that our sample metrics reflect the hidden reality of the population.{% raw %}{% endraw %}
