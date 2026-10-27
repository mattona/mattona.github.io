---
layout: page
title: Stochastic Penetration Models
description: Simulating network breaches using Bernoulli and Rademacher stochastic processes.
img: assets/img/hmw3.jpg
importance: 4
category: Statistics
permalink: /projects/homework3/
---

For this assignment, I had to simulate a stochastic penetration process. We have $m$ attackers interacting with $n$ systems, represented by independent and identically distributed (i.i.d.) increments $X_1, X_2, \dots, X_n$. 

Building the simulation in JavaScript was actually a bit of a headache. When implementing the histograms, I had to write custom logic to dynamically resize the bins, because computing the raw sum $S_n$ for $n=1000$ causes the variance to blow up, completely breaking standard charting scales.

### 1. The Mathematical Models

We are looking at two specific stochastic processes for the increment $X_i$:

1.  **Bernoulli Model:** $X_i \in \{0, 1\}$, with $P(X_i = 1) = p$. The expected value is $E[X_i] = p$ and the variance is $Var(X_i) = p(1-p)$.
2.  **Rademacher Model:** $X_i \in \{-1, 1\}$, with $P(X_i = 1) = P(X_i = -1) = 0.5$. This provides a symmetric, zero-mean baseline where $E[X_i] = 0$ and $Var(X_i) = 1$.

### Interactive Simulation Dashboard

The engine below simulates these random walks in real-time. By tweaking $n$ (time steps) and $m$ (sample trajectories), you can observe how the distributions scale according to the theoretical limits.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-4">Stochastic Trajectory Generator</h5>
    
    <!-- Controls Section -->
    <div class="d-flex flex-column align-items-center mb-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Simulation Model:</label>
        <select id="simModel" class="form-control" style="width: 65%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);" onchange="updateProbSlider(); runSimulation();">
            <option value="bernoulli">Bernoulli (0 or 1)</option>
            <option value="rademacher">Rademacher (-1 or +1)</option>
        </select>
    </div>
    
    <div class="d-flex flex-column align-items-center mb-3 mt-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Number of Attackers (m): <span id="mVal" style="color: #3498db;">100</span></label>
        <input type="range" id="mAttackers" class="custom-range" min="10" max="500" step="10" value="100" style="width: 65%;" oninput="document.getElementById('mVal').innerText = this.value; runSimulation();">
    </div>

    <div class="d-flex flex-column align-items-center mb-3 mt-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Number of Systems (n): <span id="nVal" style="color: #e74c3c;">200</span></label>
        <input type="range" id="nSystems" class="custom-range" min="10" max="1000" step="10" value="200" style="width: 65%;" oninput="document.getElementById('nVal').innerText = this.value; runSimulation();">
    </div>

    <div class="d-flex flex-column align-items-center mb-4 mt-3" id="probContainer">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Success Probability (p): <span id="pVal" style="color: #f1c40f;">0.30</span></label>
        <input type="range" id="pProb" class="custom-range" min="0.05" max="0.95" step="0.05" value="0.30" style="width: 65%;" oninput="document.getElementById('pVal').innerText = parseFloat(this.value).toFixed(2); runSimulation();">
    </div>
    
    <hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">
    
    <!-- Charts Column Section -->
    <div class="d-flex flex-column align-items-center w-100">
        
        <h6 class="font-weight-bold mb-3">Attacker Trajectories over Time</h6>
        <div class="w-100 mb-5 px-3">
            <canvas id="trajectoryChart" height="120"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Total Score ($S_n$)</h6>
        <div class="w-100 px-3 mb-5">
            <canvas id="histTotal" height="100"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Average Score ($S_n / n$)</h6>
        <div class="w-100 px-3 mb-5">
            <canvas id="histAverage" height="100"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Normalized Score ($S_n / \sqrt{n}$)</h6>
        <div class="w-100 px-3 mb-2">
            <canvas id="histNormalized" height="100"></canvas>
        </div>
        
    </div>
</div>

### 2. Analytical Results vs. Simulation

Let the total penetration score be $S_n = \sum_{i=1}^n X_i$. Running the simulation for increasing values of $n$ demonstrates three fundamental limit theorems, depending on how we scale the aggregate score.

**Total Score ($S_n$):**  
For the Bernoulli model, the expected value is $E[S_n] = np$ and the variance is $Var(S_n) = np(1-p)$. As $n$ increases, the variance grows linearly to infinity. You can see this in the first histogram: as $n$ goes up, the X-axis continuously expands to accommodate the growing spread.

**Average Score and the WLLN ($S_n / n$):**  
When we divide by $n$, the expected value remains $p$, but the variance scales down quadratically:
$$ Var\left(\frac{S_n}{n}\right) = \frac{1}{n^2} Var(S_n) = \frac{p(1-p)}{n} $$
As $n \to \infty$, this variance tends to 0. This algebraic property is the basis of the **Weak Law of Large Numbers (WLLN)**, which states that the sample mean converges in probability to the expected value. We can formalize this using Chebyshev's inequality:
$$ P\left(\left|\frac{S_n}{n} - p\right| \ge \epsilon\right) \le \frac{Var(S_n/n)}{\epsilon^2} = \frac{p(1-p)}{n\epsilon^2} \xrightarrow{n \to \infty} 0 $$
Visually, the average score histogram collapses into a single spike at exactly $p$. 

**Normalized Score and the CLT ($S_n / \sqrt{n}$):**  
To stabilize the variance, we scale by $1/\sqrt{n}$. Looking at the symmetric Rademacher model:
$$ Var\left(\frac{S_n}{\sqrt{n}}\right) = \frac{1}{n} Var(S_n) = \frac{1}{n}(n \cdot 1) = 1 $$
Because the variance remains exactly 1, the distribution doesn't collapse or blow up. According to the **Central Limit Theorem (CLT)**, the cumulative distribution function (CDF) of this normalized sum converges pointwise to the CDF of a Standard Normal distribution $\Phi(x)$. The simulation confirms this: the normalized chart maintains a stable $N(0,1)$ shape between -3 and +3.

### 3. The Effect of $m$ and Extreme Value Theory

The prompt also asks to interpret the role of $m$ (the number of attackers). While a high $m$ is necessary in the simulation just to get a smooth histogram, in a real security context, increasing $m$ fundamentally shifts the threat model.

If we face a massive number of attackers, looking at the mean $E[S_n]$ is insufficient. We need to evaluate the maximum penetration: $M_m = \max(S_n^{(1)}, \dots, S_n^{(m)})$. As $m$ increases, the probability of drawing an outlier from the extreme right tail of the normal distribution approaches 1. This behavior is studied in **Extreme Value Theory (EVT)**. Even if a single attacker has a low success probability, with $m = 10,000$ parallel attacks, it is mathematically almost certain that at least one attacker will reach a critically high penetration score simply due to variance.

<!-- Scripts for interactive tools -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
{% raw %}
<script>
let trajChartInstance = null;
let histTotalInstance = null;
let histAvgInstance = null;
let histNormInstance = null;

function updateProbSlider() {
    let model = document.getElementById("simModel").value;
    let probContainer = document.getElementById("probContainer");
    if(model === 'rademacher') {
        probContainer.style.opacity = '0.3';
        document.getElementById("pProb").disabled = true;
    } else {
        probContainer.style.opacity = '1';
        document.getElementById("pProb").disabled = false;
    }
}

function runSimulation() {
    let n = parseInt(document.getElementById('nSystems').value);
    let m = parseInt(document.getElementById('mAttackers').value);
    let p = parseFloat(document.getElementById('pProb').value);
    let model = document.getElementById('simModel').value;

    let datasets = [];
    let finalScores = { total: [], average: [], normalized: [] };

    // Generate random walks
    for (let a = 0; a < m; a++) {
        let path = [0];
        let currentScore = 0;
        
        for (let s = 1; s <= n; s++) {
            let step = 0;
            if (model === 'bernoulli') {
                step = Math.random() < p ? 1 : 0;
            } else {
                step = Math.random() < 0.5 ? 1 : -1;
            }
            currentScore += step;
            path.push(currentScore);
        }
        
        finalScores.total.push(currentScore);
        finalScores.average.push(currentScore / n);
        finalScores.normalized.push(currentScore / Math.sqrt(n));

        datasets.push({
            data: path,
            borderColor: `hsla(${Math.random() * 360}, 70%, 50%, 0.3)`,
            borderWidth: 1,
            pointRadius: 0,
            fill: false,
            tension: 0.1
        });
    }

    renderTrajectories(n, datasets);
    renderHistograms(finalScores);
}

function renderTrajectories(n, datasets) {
    const ctx = document.getElementById('trajectoryChart').getContext('2d');
    Chart.defaults.color = "#888888";

    if (trajChartInstance) trajChartInstance.destroy();

    let labels = Array.from({length: n + 1}, (_, i) => i);

    trajChartInstance = new Chart(ctx, {
        type: 'line',
        data: { labels: labels, datasets: datasets },
        options: {
            responsive: true,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: 'Systems / Steps (n)' } },
                y: { title: { display: true, text: 'Cumulative Penetration Score' } }
            },
            animation: false 
        }
    });
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

function renderSingleHistogram(ctx, chartInstance, dataToBin, titleLabel, color) {
    let binCount = Math.max(10, Math.min(40, Math.floor(dataToBin.length / 5)));
    let histData = createBins(dataToBin, binCount);

    if (chartInstance) chartInstance.destroy();

    return new Chart(ctx, {
        type: 'bar',
        data: {
            labels: histData.labels,
            datasets: [{
                label: 'Frequency',
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
                x: { title: { display: true, text: titleLabel } },
                y: { title: { display: false }, beginAtZero: true }
            },
            animation: { duration: 200 }
        }
    });
}

function renderHistograms(scores) {
    histTotalInstance = renderSingleHistogram(
        document.getElementById('histTotal').getContext('2d'),
        histTotalInstance,
        scores.total,
        'Value',
        'rgba(231, 76, 60, 0.7)' // Red
    );

    histAvgInstance = renderSingleHistogram(
        document.getElementById('histAverage').getContext('2d'),
        histAvgInstance,
        scores.average,
        'Value',
        'rgba(46, 204, 113, 0.7)' // Green
    );

    histNormInstance = renderSingleHistogram(
        document.getElementById('histNormalized').getContext('2d'),
        histNormInstance,
        scores.normalized,
        'Value',
        'rgba(155, 89, 182, 0.7)' // Purple
    );
}

// Esegui la simulazione automaticamente al caricamento
document.addEventListener("DOMContentLoaded", function() {
    updateProbSlider();
    runSimulation();
});
</script>
{% endraw %}
