---
layout: page
title: Welford's Algorithm & Sampling Distributions
description: Online variance computation and empirical verification of estimator unbiasedness and consistency across multi-system penetration trajectories.
img: assets/img/welford.jpg
importance: 4
category: Statistics
permalink: /projects/homework3/
---

In the previous simulation, each penetration attempt was modeled as a simple binary outcome. However, real-world vulnerability assessments—such as **CVSS (Common Vulnerability Scoring System)** metrics—score compromises on a continuous or discrete scale from $0$ to $10$. 

Furthermore, computing statistical moments across massive telemetry streams exposes a classic numerical analysis problem: standard two-pass variance formulas fail at scale, and naive one-pass formulas suffer from floating-point instability. This assignment addresses both the algorithmic computation of streaming moments via **Welford's recurrence** and the inferential properties of **sample mean and sample variance** across $m$ attackers probing $n$ systems.

### 1. Exercise 1: Welford's Recurrence Formulas

If we want to compute the variance of a stream of scores $x_1, x_2, \dots, x_n$ in a single pass, the textbook algebraic expansion $\sigma^2 = \frac{1}{n}\sum_{i=1}^n x_i^2 - \bar{x}_n^2$ is a trap. In IEEE 754 floating-point arithmetic, when the variance is small relative to the square of the mean, $\frac{1}{n}\sum x_i^2$ and $\bar{x}_n^2$ become two large, nearly identical numbers. Subtracting them wipes out the least significant mantissa bits—a phenomenon known as **catastrophic cancellation**—which can even yield negative variance estimates.

To compute moments stably in $O(1)$ memory, we update the mean and the **Sum of Squares of differences from the mean** ($SS_k$, often denoted $M_{2,k}$) incrementally at each step $k$.

#### Step 1: Knuth's Recurrence for the Mean

Let $\bar{x}_k = \frac{1}{k}\sum_{i=1}^k x_i$. Isolating the $k$-th observation from the previous $k-1$ terms gives:

$$\bar{x}_k = \frac{(k-1)\bar{x}_{k-1} + x_k}{k} = \bar{x}_{k-1} + \frac{x_k - \bar{x}_{k-1}}{k}$$

Defining the pre-update residual $\delta_k = x_k - \bar{x}_{k-1}$, the mean shift is simply $\bar{x}_k - \bar{x}_{k-1} = \frac{\delta_k}{k}$.

#### Step 2: Deriving Welford's Recurrence for the Sum of Squares ($SS_k$)

Define the running sum of squared deviations at step $k$ as:

$$SS_k = \sum_{i=1}^k (x_i - \bar{x}_k)^2$$

Splitting the sum at the $k$-th element and adding/subtracting $\bar{x}_{k-1}$ inside the first summation yields:

$$SS_k = \sum_{i=1}^{k-1} \left[ (x_i - \bar{x}_{k-1}) - (\bar{x}_k - \bar{x}_{k-1}) \right]^2 + (x_k - \bar{x}_k)^2$$

Expanding the square inside the sum over $i = 1, \dots, k-1$:

$$SS_k = \sum_{i=1}^{k-1} (x_i - \bar{x}_{k-1})^2 - 2(\bar{x}_k - \bar{x}_{k-1})\sum_{i=1}^{k-1}(x_i - \bar{x}_{k-1}) + (k-1)(\bar{x}_k - \bar{x}_{k-1})^2 + (x_k - \bar{x}_k)^2$$

Notice two critical simplifications:

1. The first term $\sum_{i=1}^{k-1} (x_i - \bar{x}_{k-1})^2$ is by definition $SS_{k-1}$.
2. The cross-term vanishes completely because the sum of deviations from the mean is identically zero: $\sum_{i=1}^{k-1}(x_i - \bar{x}_{k-1}) = 0$.

This leaves us with:

$$SS_k = SS_{k-1} + (k-1)(\bar{x}_k - \bar{x}_{k-1})^2 + (x_k - \bar{x}_k)^2$$

Now, substitute $\bar{x}_k - \bar{x}_{k-1} = \frac{x_k - \bar{x}_{k-1}}{k}$ and factor out one power of that difference. Notice that the remaining deviation from the *new* mean can be written as:

$$x_k - \bar{x}_k = (x_k - \bar{x}_{k-1}) - (\bar{x}_k - \bar{x}_{k-1}) = \left(1 - \frac{1}{k}\right)(x_k - \bar{x}_{k-1}) = \frac{k-1}{k}(x_k - \bar{x}_{k-1})$$

Using this relation, let's simplify the two remaining terms:

$$(k-1)(\bar{x}_k - \bar{x}_{k-1})^2 + (x_k - \bar{x}_k)^2 = \frac{k-1}{k^2}(x_k - \bar{x}_{k-1})^2 + \frac{(k-1)^2}{k^2}(x_k - \bar{x}_{k-1})^2$$

Factoring out $\frac{k-1}{k^2}(x_k - \bar{x}_{k-1})^2$:

$$= \frac{k-1}{k^2}(x_k - \bar{x}_{k-1})^2 \left[ 1 + (k - 1) \right] = \frac{k-1}{k}(x_k - \bar{x}_{k-1})^2 = (x_k - \bar{x}_{k-1})(x_k - \bar{x}_k)$$

This produces **Welford's recurrence formula for the Sum of Squares**:

$$SS_k = SS_{k-1} + (x_k - \bar{x}_{k-1})(x_k - \bar{x}_k)$$

From $SS_k$, the **unbiased sample variance** $s_k^2$ (with Bessel's correction, for $k > 1$) and the **population/ML variance** $\hat{\sigma}_k^2$ follow immediately:

$$s_k^2 = \frac{SS_k}{k - 1}, \qquad \hat{\sigma}_k^2 = \frac{SS_k}{k}$$

#### The 3-Line Core Implementation

What makes this identity so clean in code is that we compute the residual before updating the mean ($\delta_1 = x_k - \bar{x}_{k-1}$), update the mean in place, and then multiply $\delta_1$ by the post-update residual ($\delta_2 = x_k - \bar{x}_k$):

```javascript
// Core Welford update for observation x at step k (1-indexed)
let delta = x - mean;          // 1. Deviation from previous mean (x_k - mean_{k-1})
mean += delta / k;             // 2. Knuth mean update (mean_k)
ss += delta * (x - mean);      // 3. Welford SS update using new mean (x_k - mean_k)
```

### 2. Interactive Simulation Dashboard (Exercise 2)

In this experiment, $m$ independent hackers attempt to penetrate $n$ systems. Each attempt yields a severity score $X_{j,i} \in [0, 10]$ drawn from a chosen probability distribution with theoretical mean $\mu$ and variance $\sigma^2$. For every hacker $j \in \{1, \dots, m\}$, Welford's algorithm tracks the running mean and variance across their trajectory of $n$ systems.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-4">Welford Streaming & Sampling Distribution Engine</h5>

    <!-- Controls Section -->
    <div class="d-flex flex-column align-items-center mb-3">
        <label class="font-weight-bold mb-1" style="width: 75%; text-align: left;">Score Probability Distribution $X \in [0, 10]$:</label>
        <select id="distModel" class="form-control" style="width: 75%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);" onchange="updateControls(); runWelfordSimulation();">
            <option value="uniform_cont">Continuous Uniform U(0, 10) — [μ = 5.00, σ² = 8.3333]</option>
            <option value="uniform_disc">Discrete Uniform {0, 1, ..., 10} — [μ = 5.00, σ² = 10.0000]</option>
            <option value="binomial">Binomial Bin(10, p) — Tunable Skewness</option>
        </select>
    </div>

    <div class="d-flex flex-column align-items-center mb-3 mt-2" id="probContainer" style="display: none !important;">
        <label class="font-weight-bold mb-1" style="width: 75%; text-align: left;">Binomial Parameter (p): <span id="pVal" style="color: #f1c40f;">0.35</span></label>
        <input type="range" id="pProb" class="custom-range" min="0.10" max="0.90" step="0.05" value="0.35" style="width: 75%;" oninput="document.getElementById('pVal').innerText = parseFloat(this.value).toFixed(2); runWelfordSimulation();">
    </div>

    <div class="d-flex flex-column align-items-center mb-3 mt-2">
        <label class="font-weight-bold mb-1" style="width: 75%; text-align: left;">Number of Hackers (m): <span id="mVal" style="color: #3498db;">200</span></label>
        <input type="range" id="mHackers" class="custom-range" min="20" max="1000" step="20" value="200" style="width: 75%;" oninput="document.getElementById('mVal').innerText = this.value; runWelfordSimulation();">
    </div>

    <div class="d-flex flex-column align-items-center mb-3 mt-2">
        <label class="font-weight-bold mb-1" style="width: 75%; text-align: left;">Systems Attempted per Hacker (n): <span id="nVal" style="color: #e74c3c;">100</span></label>
        <input type="range" id="nSystems" class="custom-range" min="5" max="1000" step="5" value="100" style="width: 75%;" oninput="document.getElementById('nVal').innerText = this.value; runWelfordSimulation();">
    </div>

    <div class="d-flex justify-content-center mt-2 mb-4">
        <button class="btn btn-sm btn-outline-primary px-4" onclick="runWelfordSimulation()">Re-run Monte Carlo Trial</button>
    </div>

    <!-- Live Metrics Comparison Table -->
    <div class="px-4 mb-4">
        <div class="table-responsive">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th>Statistic</th>
                        <th>Theoretical Parameter</th>
                        <th>Empirical Average Across $m$ Hackers</th>
                        <th>Absolute Error</th>
                        <th>Spread Across Hackers (Std. Error)</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td class="font-weight-bold">Mean ($\mu$ vs. $\overline{\bar{X}}$)</td>
                        <td id="theoMean">5.0000</td>
                        <td id="empMean" style="color: #2ecc71; font-weight: bold;">-</td>
                        <td id="errMean">-</td>
                        <td id="seMean">-</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold">Unbiased Variance ($\sigma^2$ vs. $\overline{S^2}$, div $n-1$)</td>
                        <td id="theoVar">8.3333</td>
                        <td id="empVarUnbiased" style="color: #9b59b6; font-weight: bold;">-</td>
                        <td id="errVarUnbiased">-</td>
                        <td id="seVarUnbiased">-</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold">Biased Variance ($\sigma^2$ vs. $\overline{\hat{\sigma}^2}$, div $n$)</td>
                        <td id="theoVarBiased">8.3333</td>
                        <td id="empVarBiased" style="color: #e67e22;">-</td>
                        <td id="errVarBiased">-</td>
                        <td id="seVarBiased">-</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">

    <!-- Charts Section -->
    <div class="d-flex flex-column align-items-center w-100">
        
        <h6 class="font-weight-bold mb-1 text-center">Welford Running Mean Trajectories ($\bar{X}_k$ vs. System Step $k$)</h6>
        <p class="small text-muted mb-3 text-center">Dashed red line indicates theoretical $\mu$. Watch the funnel narrow as $k \to n$.</p>
        <div class="w-100 mb-5 px-3">
            <canvas id="meanTrajChart" height="110"></canvas>
        </div>

        <h6 class="font-weight-bold mb-1 text-center">Welford Running Variance Trajectories ($S_k^2$ vs. System Step $k$)</h6>
        <p class="small text-muted mb-3 text-center">Computed online via $SS_k / (k - 1)$ without storing past scores.</p>
        <div class="w-100 mb-5 px-3">
            <canvas id="varTrajChart" height="110"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Distribution of Final Sampling Means ($\bar{X}_n$) Across $m$ Hackers</h6>
        <div class="w-100 px-3 mb-5">
            <canvas id="histSampleMeans" height="95"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Distribution of Final Sampling Variances ($S_n^2$) Across $m$ Hackers</h6>
        <div class="w-100 px-3 mb-2">
            <canvas id="histSampleVars" height="95"></canvas>
        </div>
        
    </div>
</div>

### 3. Statistical Analysis & Conclusions

Running the experiment across different configurations of $m$ and $n$ highlights three foundational pillars of inferential statistics:

#### 1. Unbiasedness of the Estimators (Comparing Empirical Averages to $\mu$ and $\sigma^2$)

For each hacker $j$, let $\bar{X}_j = \frac{1}{n}\sum_{i=1}^n X_{j,i}$ and $S_j^2 = \frac{1}{n-1}\sum_{i=1}^n (X_{j,i} - \bar{X}_j)^2$ be their final Welford statistics after $n$ systems. Averaging across all $m$ hackers gives the grand averages:

$$\overline{\bar{X}} = \frac{1}{m}\sum_{j=1}^m \bar{X}_j \approx \mu, \qquad \overline{S^2} = \frac{1}{m}\sum_{j=1}^m S_j^2 \approx \sigma^2$$

Even when $n$ is very small (try setting $n = 5$ or $n = 10$ in the slider above and $m = 1000$), the average of the sampling means and the average of the unbiased sampling variances match the theoretical parameters $\mu$ and $\sigma^2$ remarkably well. This empirically confirms that $\bar{X}$ and $S^2$ are **unbiased estimators**:

$$E[\bar{X}] = \mu, \qquad E[S^2] = \sigma^2$$

Notice the third row in the live table above: if we divide Welford's $SS_n$ by $n$ instead of $n-1$, the estimator $\hat{\sigma}^2 = SS_n / n$ systematically underestimates $\sigma^2$ because $E[\hat{\sigma}^2] = \frac{n-1}{n}\sigma^2$. At $n=5$, dividing by $n$ introduces a massive $20\%$ downward bias! Dividing by $n-1$ (**Bessel's correction**) compensates for the single degree of freedom lost by estimating $\mu$ with $\bar{X}$ from the same trajectory.

#### 2. Why This Matters in Inferential Statistics

In real cybersecurity telemetry or scientific sampling, we almost never observe the entire population of all possible attack trajectories ($m \to \infty$), nor do we know the true underlying data-generating parameters $\mu$ and $\sigma^2$. Often, we only have a finite log of $n$ observations from a single campaign.

* **Unbiasedness** guarantees that our estimation procedure has **zero systematic error**: on average, our estimates neither overshoot nor undershoot the true threat level.
* Knowing the exact relationship between sample statistics and population parameters allows us to construct **confidence intervals**, quantify uncertainty via the **Standard Error** ($SE = s / \sqrt{n}$), and perform hypothesis testing (such as a $t$-test or $\chi^2$-test) to detect whether an attacker's behavior has statistically shifted.

#### 3. The Effect of Increasing $n$ (Consistency & Variance Reduction)

If you drag the $n$ slider from $n = 10$ up to $n = 1000$, two distinct phenomena appear immediately in the histograms and the trajectory plots:

* **Contraction of Sampling Means:** The variance of the sampling mean across hackers is governed by:

$$Var(\bar{X}_n) = \frac{\sigma^2}{n} \implies SD(\bar{X}_n) = \frac{\sigma}{\sqrt{n}}$$

As $n$ increases, the spread of the green histogram shrinks at a rate of $1/\sqrt{n}$, and by the **Central Limit Theorem**, its shape converges to a tight Gaussian bell curve centered at $\mu$.

* **Contraction of Sampling Variances:** Similarly, the variance of the sample variance scales inversely with $n$:

$$Var(S_n^2) = \frac{1}{n}\left(\mu_4 - \frac{n-3}{n-1}\sigma^4\right) = \mathcal{O}\left(\frac{1}{n}\right)$$

where $\mu_4 = E[(X - \mu)^4]$ is the fourth central moment. For small $n$, the purple histogram of $S_n^2$ is noticeably right-skewed and wide; as $n$ grows, its variance tends to $0$ and it concentrates tightly around $\sigma^2$.

In inferential terms, because both estimators are unbiased *and* their variances converge to zero as $n \to \infty$, both $\bar{X}_n$ and $S_n^2$ are **consistent estimators** ($\bar{X}_n \xrightarrow{P} \mu$ and $S_n^2 \xrightarrow{P} \sigma^2$). Monitoring more systems per hacker directly translates into higher precision and lower estimation uncertainty.

<!-- Scripts for interactive tools -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
{% raw %}
<script>
let meanTrajChartInstance = null;
let varTrajChartInstance = null;
let histMeansInstance = null;
let histVarsInstance = null;

function updateControls() {
    let dist = document.getElementById("distModel").value;
    let probContainer = document.getElementById("probContainer");
    if (dist === "binomial") {
        probContainer.style.setProperty("display", "flex", "important");
    } else {
        probContainer.style.setProperty("display", "none", "important");
    }
}

// Draw a random score between 0 and 10 from the selected distribution
function drawScore(dist, p) {
    if (dist === "uniform_cont") {
        return Math.random() * 10.0;
    } else if (dist === "uniform_disc") {
        return Math.floor(Math.random() * 11); // 0, 1, ..., 10
    } else if (dist === "binomial") {
        let successes = 0;
        for (let i = 0; i < 10; i++) {
            if (Math.random() < p) successes++;
        }
        return successes;
    }
    return 0;
}

// Return theoretical mean (mu) and variance (sigma^2)
function getTheoreticalMoments(dist, p) {
    if (dist === "uniform_cont") {
        // U(0, 10) -> mean = 5, var = (10 - 0)^2 / 12 = 100 / 12 = 8.3333
        return { mu: 5.0, sigma2: 100.0 / 12.0 };
    } else if (dist === "uniform_disc") {
        // Discrete uniform on {0, ..., 10} (N = 11 outcomes) -> var = (N^2 - 1) / 12 = 120 / 12 = 10
        return { mu: 5.0, sigma2: 10.0 };
    } else if (dist === "binomial") {
        // Bin(10, p) -> mean = 10p, var = 10p(1-p)
        return { mu: 10.0 * p, sigma2: 10.0 * p * (1.0 - p) };
    }
    return { mu: 0, sigma2: 0 };
}

function computeArrayStats(arr) {
    let mean = 0;
    let ss = 0;
    for (let i = 0; i < arr.length; i++) {
        let k = i + 1;
        let delta = arr[i] - mean;
        mean += delta / k;
        ss += delta * (arr[i] - mean);
    }
    let sd = arr.length > 1 ? Math.sqrt(ss / (arr.length - 1)) : 0;
    return { mean, sd };
}

function runWelfordSimulation() {
    let dist = document.getElementById("distModel").value;
    let p = parseFloat(document.getElementById("pProb").value);
    let m = parseInt(document.getElementById("mHackers").value);
    let n = parseInt(document.getElementById("nSystems").value);

    let theo = getTheoreticalMoments(dist, p);

    let meanTrajectories = [];
    let varTrajectories = [];
    let finalMeans = [];
    let finalVarsUnbiased = [];
    let finalVarsBiased = [];

    // Cap plotted trajectories to 60 for smooth rendering while computing full m for statistics
    let maxPlottedTrajectories = Math.min(m, 60);

    for (let h = 0; h < m; h++) {
        let mean = 0.0;
        let ss = 0.0;

        let recordPath = h < maxPlottedTrajectories;
        let meanPath = recordPath ? [] : null;
        let varPath = recordPath ? [] : null;

        for (let k = 1; k <= n; k++) {
            let x = drawScore(dist, p);

            // --- EX. 1: CORE 3-LINE WELFORD ALGORITHM ---
            let delta = x - mean;
            mean += delta / k;
            ss += delta * (x - mean);
            // --------------------------------------------

            if (recordPath) {
                meanPath.push(mean);
                varPath.push(k > 1 ? ss / (k - 1) : 0);
            }
        }

        finalMeans.push(mean);
        finalVarsUnbiased.push(ss / (n - 1));
        finalVarsBiased.push(ss / n);

        if (recordPath) {
            let hue = (h * 137.5) % 360;
            meanTrajectories.push({
                data: meanPath,
                borderColor: `hsla(${hue}, 70%, 55%, 0.28)`,
                borderWidth: 1,
                pointRadius: 0,
                fill: false,
                tension: 0.1
            });
            varTrajectories.push({
                data: varPath,
                borderColor: `hsla(${hue}, 70%, 55%, 0.28)`,
                borderWidth: 1,
                pointRadius: 0,
                fill: false,
                tension: 0.1
            });
        }
    }

    // Add theoretical reference line to both trajectory charts
    meanTrajectories.unshift({
        data: Array(n).fill(theo.mu),
        borderColor: "rgba(231, 76, 60, 0.95)",
        borderWidth: 2,
        borderDash: [6, 4],
        pointRadius: 0,
        fill: false
    });

    varTrajectories.unshift({
        data: Array(n).fill(theo.sigma2),
        borderColor: "rgba(231, 76, 60, 0.95)",
        borderWidth: 2,
        borderDash: [6, 4],
        pointRadius: 0,
        fill: false
    });

    // Compute averages across the m hackers
    let statsMean = computeArrayStats(finalMeans);
    let statsVarUnbiased = computeArrayStats(finalVarsUnbiased);
    let statsVarBiased = computeArrayStats(finalVarsBiased);

    // Update Summary Table
    document.getElementById("theoMean").innerText = theo.mu.toFixed(4);
    document.getElementById("empMean").innerText = statsMean.mean.toFixed(4);
    document.getElementById("errMean").innerText = Math.abs(statsMean.mean - theo.mu).toFixed(4);
    document.getElementById("seMean").innerText = statsMean.sd.toFixed(4);

    document.getElementById("theoVar").innerText = theo.sigma2.toFixed(4);
    document.getElementById("empVarUnbiased").innerText = statsVarUnbiased.mean.toFixed(4);
    document.getElementById("errVarUnbiased").innerText = Math.abs(statsVarUnbiased.mean - theo.sigma2).toFixed(4);
    document.getElementById("seVarUnbiased").innerText = statsVarUnbiased.sd.toFixed(4);

    document.getElementById("theoVarBiased").innerText = theo.sigma2.toFixed(4);
    document.getElementById("empVarBiased").innerText = statsVarBiased.mean.toFixed(4);
    document.getElementById("errVarBiased").innerText = Math.abs(statsVarBiased.mean - theo.sigma2).toFixed(4);
    document.getElementById("seVarBiased").innerText = statsVarBiased.sd.toFixed(4);

    renderLineChart("meanTrajChart", meanTrajChartInstance, n, meanTrajectories, "Running Sample Mean", instance => meanTrajChartInstance = instance);
    renderLineChart("varTrajChart", varTrajChartInstance, n, varTrajectories, "Running Sample Variance (s²)", instance => varTrajChartInstance = instance);

    histMeansInstance = renderHistogram(
        document.getElementById("histSampleMeans").getContext("2d"),
        histMeansInstance,
        finalMeans,
        `Sample Mean across ${m} Hackers (Theoretical μ = ${theo.mu.toFixed(2)})`,
        "rgba(46, 204, 113, 0.7)"
    );

    histVarsInstance = renderHistogram(
        document.getElementById("histSampleVars").getContext("2d"),
        histVarsInstance,
        finalVarsUnbiased,
        `Sample Variance s² across ${m} Hackers (Theoretical σ² = ${theo.sigma2.toFixed(2)})`,
        "rgba(155, 89, 182, 0.7)"
    );
}

function renderLineChart(canvasId, chartInstance, n, datasets, yLabel, setInstanceCallback) {
    const ctx = document.getElementById(canvasId).getContext("2d");
    Chart.defaults.color = "#888888";

    if (chartInstance) chartInstance.destroy();

    let labels = Array.from({length: n}, (_, i) => i + 1);

    let newChart = new Chart(ctx, {
        type: "line",
        data: { labels: labels, datasets: datasets },
        options: {
            responsive: true,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: "Systems Attempted (k)" } },
                y: { title: { display: true, text: yLabel } }
            },
            animation: false
        }
    });
    setInstanceCallback(newChart);
}

function createBins(data, numBins) {
    let min = Math.min(...data);
    let max = Math.max(...data);
    if (min === max) { min -= 0.5; max += 0.5; }

    let binWidth = (max - min) / numBins;
    let counts = new Array(numBins).fill(0);

    for (let val of data) {
        let idx = Math.floor((val - min) / binWidth);
        if (idx >= numBins) idx = numBins - 1;
        counts[idx]++;
    }

    let labels = [];
    for (let i = 0; i < numBins; i++) {
        let binCenter = min + (i * binWidth) + (binWidth / 2);
        labels.push(binCenter.toFixed(2));
    }
    return { labels, counts };
}

function renderHistogram(ctx, chartInstance, dataToBin, xTitle, color) {
    let binCount = Math.max(12, Math.min(35, Math.floor(Math.sqrt(dataToBin.length) * 1.8)));
    let histData = createBins(dataToBin, binCount);

    if (chartInstance) chartInstance.destroy();

    return new Chart(ctx, {
        type: "bar",
        data: {
            labels: histData.labels,
            datasets: [{
                label: "Hackers Count",
                data: histData.counts,
                backgroundColor: color,
                borderColor: color,
                borderWidth: 1,
                borderRadius: 2
            }]
        },
        options: {
            responsive: true,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: xTitle } },
                y: { title: { display: true, text: "Frequency" }, beginAtZero: true }
            },
            animation: { duration: 150 }
        }
    });
}

document.addEventListener("DOMContentLoaded", function() {
    updateControls();
    runWelfordSimulation();
});
</script>
{% endraw %}
