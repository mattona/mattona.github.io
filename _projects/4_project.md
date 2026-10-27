---
layout: page
title: Stochastic Penetration Models
description: Simulating network breaches using Bernoulli and Rademacher stochastic processes.
img: assets/img/hmw4.jpg
importance: 4
category: Statistics
---

At first glance, modeling a cyberattack as a simple coin flip feels overly reductive. A real penetration test involves exploiting specific CVEs, chaining vulnerabilities, and bypassing firewalls. However, when trying to evaluate risk across a massive infrastructure, individual technical details become noise. 

For this assignment, I modeled the aggregate penetration process of a network. We have $m$ independent attackers trying to compromise a sequence of $n$ systems (or moving through $n$ security layers). At each step, the attacker either succeeds or fails. By zooming out and running this stochastic process thousands of times, we can observe fundamental laws of probability emerging from the chaos.

### The Two Stochastic Models

To simulate the trajectory of an attacker, we use a stochastic process with independent and identically distributed (i.i.d.) increments $X_1, X_2, \dots, X_n$. I explored two specific variations:

1.  **The Bernoulli Model:** This is the realistic scenario. At each step, the attacker succeeds $X_i = 1$ with probability $p$, or fails $X_i = 0$ with probability $1-p$.
2.  **The Rademacher Model:** This is a theoretical, symmetric model used to study zero-mean random walks. The attacker either advances $X_i = +1$ or retreats $X_i = -1$ with equal probability ($p = 0.5$). While hackers don't usually "un-hack" a system, this symmetric boundary allows us to perfectly isolate and observe the Central Limit Theorem in action.

### Interactive Simulation Dashboard

I built the engine below to simulate these random walks in real-time. The charts are generated automatically based on default parameters, but you can adjust the number of systems $n$ (time steps), the number of attackers $m$ (sample trajectories), and the model type to see how the distributions behave dynamically.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);">
    <h5 class="text-center mb-4">Stochastic Trajectory Generator</h5>
    
    <!-- Controls Section -->
    <div class="d-flex flex-column align-items-center mb-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Simulation Model:</label>
        <select id="simModel" class="form-control" style="width: 65%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);" onchange="updateProbSlider()">
            <option value="bernoulli">Bernoulli (0 or 1)</option>
            <option value="rademacher">Rademacher (-1 or +1)</option>
        </select>
    </div>
    
    <div class="d-flex flex-column align-items-center mb-3 mt-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Number of Attackers (m): <span id="mVal" style="color: #3498db;">100</span></label>
        <input type="range" id="mAttackers" class="custom-range" min="10" max="500" step="10" value="100" style="width: 65%;" oninput="document.getElementById('mVal').innerText = this.value">
    </div>

    <div class="d-flex flex-column align-items-center mb-3 mt-3">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Number of Systems (n): <span id="nVal" style="color: #e74c3c;">200</span></label>
        <input type="range" id="nSystems" class="custom-range" min="10" max="1000" step="10" value="200" style="width: 65%;" oninput="document.getElementById('nVal').innerText = this.value">
    </div>

    <div class="d-flex flex-column align-items-center mb-4 mt-3" id="probContainer">
        <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Success Probability (p): <span id="pVal" style="color: #f1c40f;">0.30</span></label>
        <input type="range" id="pProb" class="custom-range" min="0.05" max="0.95" step="0.05" value="0.30" style="width: 65%;" oninput="document.getElementById('pVal').innerText = parseFloat(this.value).toFixed(2)">
    </div>
    
    <div class="text-center mb-4">
        <button onclick="runSimulation()" class="btn font-weight-bold" style="background-color: #3498db; color: #fff; width: 250px; font-size: 1.1em; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.2);">
            Run Simulation
        </button>
    </div>
    
    <hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">
    
    <!-- Charts Column Section -->
    <div class="d-flex flex-column align-items-center w-100">
        
        <h6 class="font-weight-bold mb-3">Attacker Trajectories over Time</h6>
        <div class="w-100 mb-4 px-3">
            <canvas id="trajectoryChart" height="120"></canvas>
        </div>

        <div class="d-flex flex-column align-items-center w-100 mt-4 mb-3">
            <label class="font-weight-bold mb-1" style="width: 65%; text-align: left;">Analyze Final Distribution:</label>
            <select id="distType" class="form-control" style="width: 65%; background-color: rgba(128,128,128,0.05); color: inherit; border: 1px solid rgba(150,150,150,0.5);" onchange="updateHistogram()">
                <option value="total">Total Score ($S_n$)</option>
                <option value="average">Average Score ($S_n / n$)</option>
                <option value="normalized">Normalized Score ($S_n / \sqrt{n}$)</option>
            </select>
        </div>

        <div class="w-100 px-3 mb-2">
            <canvas id="histogramChart" height="120"></canvas>
        </div>
        
    </div>
</div>

### Interpreting the Statistics

Running the simulations repeatedly and switching between the distribution views made the theoretical formulas click for me. Depending on how we scale the aggregate penetration score $S_n$, the mathematical behavior of the system changes entirely.

**1. The Law of Large Numbers ($S_n / n$)**  
If you select the *Average Score* in the dashboard and crank $n$ up to 1000, you are witnessing the Law of Large Numbers (LLN). The LLN states that as the number of trials increases, the sample mean converges in probability to the expected value. In the Bernoulli model with $p = 0.3$, the histogram of $S_n/n$ becomes an incredibly tight spike exactly at $0.3$. In a security context, this means that while a single attacker's success is unpredictable, the overall penetration *rate* against a massive cluster of servers is virtually guaranteed to match the baseline vulnerability probability. The variance of this average goes to zero.

**2. The Central Limit Theorem ($S_n / \sqrt{n}$)**  
While the average score stabilizes, the absolute number of hacked systems $S_n$ does not. The variance of the total score grows linearly with $n$. If we apply the specific normalization factor of $1/\sqrt{n}$, we prevent the variance from blowing up to infinity or shrinking to zero. 
This is where the Rademacher model shines. Because it has a mean of zero and a variance of 1, plotting the *Normalized Score* ($S_n / \sqrt{n}$) visually proves the Central Limit Theorem (CLT). No matter what the individual discrete steps look like, the aggregate distribution converges precisely to the continuous Standard Normal Distribution $N(0,1)$. The bell curve emerges perfectly from the binary randomness.

<!-- Scripts for interactive tools -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
{% raw %}
<script>
let trajChartInstance = null;
let histChartInstance = null;
let currentFinalScores = { total: [], average: [], normalized: [] };

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
    currentFinalScores = { total: [], average: [], normalized: [] };

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
        
        // Save final states
        currentFinalScores.total.push(currentScore);
        currentFinalScores.average.push(currentScore / n);
        currentFinalScores.normalized.push(currentScore / Math.sqrt(n));

        // Downsample lines slightly if rendering gets too heavy (visual optimization)
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
    updateHistogram();
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
            animation: false // Disable animation for performance with many lines
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

function updateHistogram() {
    if(currentFinalScores.total.length === 0) return; // Not run yet

    let type = document.getElementById('distType').value;
    let dataToBin = currentFinalScores[type];
    
    // Choose dynamic bin count based on sample size, clamp between 10 and 40
    let binCount = Math.max(10, Math.min(40, Math.floor(dataToBin.length / 5)));
    let histData = createBins(dataToBin, binCount);

    const ctx = document.getElementById('histogramChart').getContext('2d');

    if (histChartInstance) histChartInstance.destroy();

    let xLabel = type === 'total' ? 'Total Score ($S_n$)' : 
                 type === 'average' ? 'Average Score ($S_n / n$)' : 
                 'Normalized Score ($S_n / \\sqrt{n}$)';

    histChartInstance = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: histData.labels,
            datasets: [{
                label: 'Frequency of Attackers',
                data: histData.counts,
                backgroundColor: 'rgba(46, 204, 113, 0.7)',
                borderColor: 'rgba(46, 204, 113, 1)',
                borderWidth: 1,
                borderRadius: 2
            }]
        },
        options: {
            responsive: true,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: xLabel } },
                y: { title: { display: true, text: 'Count' }, beginAtZero: true }
            },
            animation: { duration: 400 }
        }
    });
}

// Inizializza slider ed esegue la simulazione automaticamente al caricamento
document.addEventListener("DOMContentLoaded", function() {
    updateProbSlider();
    runSimulation();
});
</script>
{% endraw %}
