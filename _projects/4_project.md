---
layout: page
title: Stochastic Penetration Models
description: Simulating network breaches using Bernoulli and Rademacher stochastic processes.
img: assets/img/hmw4.jpg
importance: 4
category: Statistics
permalink: /projects/homework3/
---

At first glance, modeling a cyberattack as a simple coin flip feels overly reductive. A real penetration test involves exploiting specific CVEs, chaining vulnerabilities, and bypassing firewalls. However, when trying to evaluate risk across a massive infrastructure, individual technical details become noise. 

For this assignment, I modeled the aggregate penetration process of a network. We have $m$ independent attackers trying to compromise a sequence of $n$ systems (or moving through $n$ security layers). At each step, the attacker either succeeds or fails. By zooming out and running this stochastic process thousands of times, we can observe fundamental laws of probability emerging from the chaos.

### The Two Stochastic Models

To simulate the trajectory of an attacker, we use a stochastic process with independent and identically distributed (i.i.d.) increments $X_1, X_2, \dots, X_n$. I explored two specific variations:

1.  **The Bernoulli Model:** This is the realistic scenario. At each step, the attacker succeeds $X_i = 1$ with probability $p$, or fails $X_i = 0$ with probability $1-p$.
2.  **The Rademacher Model:** This is a theoretical, symmetric model used to study zero-mean random walks. The attacker either advances $X_i = +1$ or retreats $X_i = -1$ with equal probability ($p = 0.5$). While hackers don't usually "un-hack" a system, this symmetric boundary allows us to perfectly isolate and observe the Central Limit Theorem in action.

### Interactive Simulation Dashboard

I built the engine below to simulate these random walks in real-time. The charts are generated automatically based on default parameters. By tweaking $n$ (time steps) and $m$ (sample trajectories), you can observe how the distributions scale mathematically.

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
        <p style="font-size: 0.85em; color: #888;" class="mb-2">Variance grows with n.</p>
        <div class="w-100 px-3 mb-5">
            <canvas id="histTotal" height="100"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Average Score ($S_n / n$)</h6>
        <p style="font-size: 0.85em; color: #888;" class="mb-2">Variance shrinks to zero (Law of Large Numbers).</p>
        <div class="w-100 px-3 mb-5">
            <canvas id="histAverage" height="100"></canvas>
        </div>

        <h6 class="font-weight-bold mb-2 text-center">Normalized Score ($S_n / \sqrt{n}$)</h6>
        <p style="font-size: 0.85em; color: #888;" class="mb-2">Variance remains stable (Central Limit Theorem).</p>
        <div class="w-100 px-3 mb-2">
            <canvas id="histNormalized" height="100"></canvas>
        </div>
        
    </div>
</div>

### Interpreting the Statistics

Running the simulations repeatedly makes the theoretical formulas click. Notice that the three bar charts share the exact same bell shape. This is because they represent the identical underlying data ($S_n$), just scaled by a constant. The mathematical magic lies entirely in the **X-axis scale**.

**1. The Law of Large Numbers ($S_n / n$)**  
If you increase $n$ up to 1000, you are witnessing the Law of Large Numbers (LLN). Look at the X-axis for the *Average Score*: the spread collapses into an incredibly tight cluster exactly at $p$. The variance of this average goes to zero. In a security context, this means that while a single attacker's success is unpredictable, the overall penetration *rate* against a massive cluster of servers is virtually guaranteed to match the baseline vulnerability probability. 

**2. The Central Limit Theorem ($S_n / \sqrt{n}$)**  
While the average score stabilizes, the absolute number of hacked systems $S_n$ does not (its X-axis expands infinitely). If we apply the specific normalization factor of $1/\sqrt{n}$, we prevent the variance from blowing up to infinity or shrinking to zero. 
This is where the Rademacher model shines. Because it has a mean of zero and a variance of 1, plotting the *Normalized Score* ($S_n / \sqrt{n}$) visually proves the Central Limit Theorem (CLT). As you increase $n$, the X-axis of the normalized chart remains perfectly stable between -3 and +3, and the bell curve emerges perfectly from the binary randomness.

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
