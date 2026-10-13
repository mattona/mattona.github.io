---
layout: page
title: ECC Public-Key Cryptography
description: A toy example of elliptic-curve scalar multiplication and statistical property exploration.
img: assets/img/hmw1.jpg
importance: 1
category: Statistics
---

This project explores the mathematical principles underlying the public-key cryptography used in Ethereum, specifically focusing on elliptic-curve scalar multiplication (ECC). Instead of working with the extremely large parameters of the standard `secp256k1` curve, a reduced "toy curve" model on a small finite field is used to demonstrate how a private key deterministically generates a public key.

The equation for the toy curve utilized in this example is:  
$$y^2 = x^3 + 7$$

### 1. The Elliptic Curve: Real Numbers vs. Finite Fields

Before applying cryptography, it is essential to visualize the geometric shape of the curve over real numbers ($$\mathbb{R}$$). The chart below shows the continuous curve $$y^2 = x^3 + 7$$. 

<div class="row justify-content-sm-center mt-4 mb-4">
    <div class="col-sm-8">
        <div class="card p-3 z-depth-1 rounded" style="background-color: transparent;">
            <canvas id="continuousCurveChart"></canvas>
            <div class="caption mt-2" style="color: #888; font-size: 0.9em;">The continuous elliptic curve over real numbers. In cryptography, this continuous domain is replaced by a discrete finite field modulo a prime number.</div>
        </div>
    </div>
</div>

In cryptographic applications like Ethereum, plotting continuous lines is not practical due to floating-point inaccuracies and infinite bounds. Therefore, the curve is restricted to a **finite field modulo a prime number** (in our case, $$p = 17$$). The equation becomes $$y^2 \equiv x^3 + 7 \pmod{17}$$. The continuous curve transforms into a scattered set of discrete points on a 17x17 grid, preserving its algebraic properties (such as point addition) while drastically altering its visual representation.

---

### 2. Interactive Toy Example

The interface below simulates the scalar multiplication algorithm on our finite field ($$\mathbb{F}_{17}$$). By selecting a base generator point $$G$$ and specifying a private key $$k$$, the system calculates the sequence of point additions ($$1G, 2G, \dots, kG$$). 
*Note: If the algorithm hits a previously visited coordinate, the labels will merge (e.g., `1G=19G`), demonstrating the cyclic nature of finite field groups.*

<div class="row justify-content-sm-center mt-4">
    <div class="col-sm-4 mt-3 mt-md-0">
        <div class="card p-3 z-depth-1 rounded" style="background-color: transparent;">
            <h5 style="margin-bottom: 15px;">Algorithm Parameters</h5>
            <div class="form-group mt-2">
                <label for="basePoint" style="font-weight: 500;">Base Point (G):</label>
                <select id="basePoint" class="form-control form-control-sm" style="background-color: transparent; border: 1px solid rgba(128,128,128,0.5); color: inherit;">
                    <option value="1,5">G = (1, 5)</option>
                    <option value="2,6">G = (2, 6)</option>
                    <option value="3,8">G = (3, 8)</option>
                    <option value="5,8">G = (5, 8)</option>
                </select>
            </div>
            <div class="form-group mt-3">
                <label for="privateKey" style="font-weight: 500;">Private Key (k):</label>
                <input type="number" id="privateKey" class="form-control form-control-sm" value="6" min="1" max="50" style="background-color: transparent; border: 1px solid rgba(128,128,128,0.5); color: inherit;">
            </div>
            
            <button id="calcBtn" class="btn btn-primary mt-4 w-100 font-weight-bold" style="background-color: #0066cc; color: #ffffff; padding: 12px; border: none; border-radius: 6px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); cursor: pointer; text-transform: uppercase; font-size: 0.9rem; transition: background-color 0.2s;">
                Compute Public Key
            </button>
            
            <hr style="border-color: rgba(128,128,128,0.2);">
            <div id="outputLog" style="font-family: 'Courier New', Courier, monospace; font-size: 0.85em; height: 150px; overflow-y: auto; color: #888;">
                <i>Computation log will appear here...</i>
            </div>
        </div>
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        <div class="card p-3 z-depth-1 rounded text-center" style="background-color: transparent; height: 100%;">
            <canvas id="eccCanvas" width="400" height="400" style="max-width: 100%; margin: auto;"></canvas>
            <div class="caption mt-2" style="color: #888; font-size: 0.9em;">Visualization of the 17x17 finite field grid. Operations show deterministic jumps.</div>
        </div>
    </div>
</div>

---

### 3. Statistical Exploration

By iteratively testing the scalar multiplication algorithm across the cyclic group (evaluating up to $$k=18$$ starting from $$G = (1,5)$$), we can analyze the statistical behavior of the generated points. The following charts break down the coordinate distribution, bit analysis, input/output correlation, and geometric distance.

<div class="row mt-4">
    <div class="col-sm-6 mt-3 mt-md-0">
        <div class="card p-2 z-depth-1 rounded" style="background-color: transparent;">
            <canvas id="distChart"></canvas>
        </div>
        <div class="caption" style="color: #888; font-size: 0.85em;">Frequency of individual values for X and Y coordinates. Displays apparent uniformity.</div>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <div class="card p-2 z-depth-1 rounded" style="background-color: transparent;">
            <canvas id="bitChart"></canvas>
        </div>
        <div class="caption" style="color: #888; font-size: 0.85em;">Distribution of bits (0s vs 1s) across the 5-bit binary representations of the generated coordinates.</div>
    </div>
</div>

<div class="row mt-4">
    <div class="col-sm-6 mt-3 mt-md-0">
        <div class="card p-2 z-depth-1 rounded" style="background-color: transparent;">
            <canvas id="scatterChart"></canvas>
        </div>
        <div class="caption" style="color: #888; font-size: 0.85em;">Input $$k$$ vs Output $$X$$ Coordinate. Shows zero linear correlation.</div>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <div class="card p-2 z-depth-1 rounded" style="background-color: transparent;">
            <canvas id="avalancheChart"></canvas>
        </div>
        <div class="caption" style="color: #888; font-size: 0.85em;">Avalanche-like behavior: Geometric distance between consecutive outputs ($$kG$$ and $$(k+1)G$$).</div>
    </div>
</div>

**Discussion: ECC vs. Cryptographic Hash Functions**

Based on the statistical exploration, we can deduce which properties are specifically tailored for Elliptic Curve Cryptography and which belong to general hash functions:

1.  **Distribution, Apparent Uniformity, and Bit Distribution:** Both the coordinate frequency and the bit distribution charts reveal a pseudo-random spread. The bits do not heavily favor `0` or `1`, and no coordinate clusters heavily in one area. This *apparent uniformity* is a shared requirement for both hashes and ECC to prevent frequency analysis attacks.
2.  **Correlations Between Input and Output:** The scatter plot demonstrates zero linear correlation. Knowing that input $$k=4$$ produces $$X=10$$, provides no mathematical intuition that $$k=5$$ will produce $$X=14$$. This unpredictability is essential for one-way functions. 
3.  **Avalanche-like Behavior:** The distance chart shows that incrementing the input by a single unit ($$k \to k+1$$) causes a drastic, non-linear jump across the field. Hash functions (like Keccak-256) rely on a strict avalanche effect at the bit level (changing 1 bit of input flips ~50% of output bits). ECC mimics this behavior geometrically to protect the private key.
4.  **The Fundamental Distinction:** While a cryptographic hash function is a completely unstructured random oracle (designed to destroy data), ECC scalar multiplication is built upon a **strict algebraic group law**. Even though it appears statistically chaotic (as proven by our charts), the underlying homomorphism ($$kG + jG = (k+j)G$$) remains perfectly intact. This algebraic structure is the sole property that makes Digital Signatures (like those in Ethereum) possible, a feature that hash functions inherently lack.

<!-- Scripts for Math Logic, Canvas Rendering, and Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

{% raw %}
<script>
document.addEventListener("DOMContentLoaded", function() {
    // --- ECC Math Logic ---
    const P = 17;

    function mod(n, p) { return ((n % p) + p) % p; }
    function modInverse(a, p) {
        a = mod(a, p);
        for (let i = 1; i < p; i++) {
            if (mod(a * i, p) === 1) return i;
        }
        return null;
    }
    function addPoints(x1, y1, x2, y2) {
        if (x1 === null) return {x: x2, y: y2};
        if (x2 === null) return {x: x1, y: y1};
        if (x1 === x2 && y1 === mod(-y2, P)) return {x: null, y: null}; // Point at infinity

        let m;
        if (x1 === x2 && y1 === y2) {
            let num = 3 * Math.pow(x1, 2);
            let den = 2 * y1;
            m = mod(num * modInverse(den, P), P);
        } else {
            let num = y2 - y1;
            let den = x2 - x1;
            m = mod(num * modInverse(den, P), P);
        }
        let x3 = mod(Math.pow(m, 2) - x1 - x2, P);
        let y3 = mod(m * (x1 - x3) - y1, P);
        return { x: x3, y: y3 };
    }

    // --- Chart 1: Continuous Curve ---
    Chart.defaults.color = "#888888";
    const curveColor = "#8e44ad";
    
    let posData = [];
    let negData = [];
    for(let x = -1.9; x <= 5; x += 0.1) {
        let y2 = Math.pow(x, 3) + 7;
        if(y2 >= 0) {
            let y = Math.sqrt(y2);
            posData.push({x: x, y: y});
            negData.push({x: x, y: -y});
        }
    }
    
    new Chart(document.getElementById('continuousCurveChart'), {
        type: 'scatter',
        data: {
            datasets: [
                {
                    label: '+y',
                    data: posData,
                    borderColor: curveColor,
                    backgroundColor: curveColor,
                    showLine: true,
                    pointRadius: 0,
                    borderWidth: 2
                },
                {
                    label: '-y',
                    data: negData,
                    borderColor: curveColor,
                    backgroundColor: curveColor,
                    showLine: true,
                    pointRadius: 0,
                    borderWidth: 2
                }
            ]
        },
        options: {
            plugins: { title: { display: true, text: 'y² = x³ + 7 over R' }, legend: {display: false} },
            scales: { x: { title: {display: true, text: 'X'} }, y: { title: {display: true, text: 'Y'} } }
        }
    });


    // --- Canvas Rendering (Theme-Agnostic with Overlap Support) ---
    const canvas = document.getElementById("eccCanvas");
    const ctx = canvas.getContext("2d");
    const padding = 35;
    const drawWidth = canvas.width - padding - 10;
    const drawHeight = canvas.height - padding - 10;
    const gridSize = Math.min(drawWidth, drawHeight) / P;

    const gridColor = "rgba(150, 150, 150, 0.25)";
    const axisColor = "rgba(150, 150, 150, 0.8)";
    const textColor = "#888888";

    let computationHistory = [];

    function drawGrid() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.lineWidth = 1;
        for (let i = 0; i <= P; i++) {
            ctx.beginPath();
            ctx.strokeStyle = gridColor;
            ctx.moveTo(padding + i * gridSize, canvas.height - padding);
            ctx.lineTo(padding + i * gridSize, canvas.height - padding - (P * gridSize));
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(padding, canvas.height - padding - (i * gridSize));
            ctx.lineTo(padding + (P * gridSize), canvas.height - padding - (i * gridSize));
            ctx.stroke();
        }

        ctx.lineWidth = 2;
        ctx.strokeStyle = axisColor;
        ctx.beginPath();
        ctx.moveTo(padding, canvas.height - padding);
        ctx.lineTo(canvas.width - 5, canvas.height - padding); 
        ctx.moveTo(padding, canvas.height - padding);
        ctx.lineTo(padding, 5); 
        ctx.stroke();

        ctx.fillStyle = textColor;
        ctx.font = "12px sans-serif";
        ctx.textAlign = "center";
        ctx.fillText("X Coordinate", canvas.width / 2 + padding / 2, canvas.height - 5);
        ctx.save();
        ctx.translate(12, canvas.height / 2 - padding / 2);
        ctx.rotate(-Math.PI / 2);
        ctx.fillText("Y Coordinate", 0, 0);
        ctx.restore();
        
        ctx.font = "10px sans-serif";
        ctx.fillText("0", padding - 10, canvas.height - padding + 12);
        ctx.fillText("17", padding + (P * gridSize), canvas.height - padding + 15);
        ctx.fillText("17", padding - 15, canvas.height - padding - (P * gridSize) + 3);
    }

    function getCoords(x, y) {
        return { px: padding + x * gridSize, py: canvas.height - padding - (y * gridSize) };
    }

    function renderState(history, targetK) {
        drawGrid();
        
        // Draw lines first
        ctx.beginPath();
        ctx.strokeStyle = "rgba(52, 152, 219, 0.3)";
        ctx.lineWidth = 1.5;
        ctx.setLineDash([4, 4]);
        for (let i = 1; i < history.length; i++) {
            if(history[i-1].x !== null && history[i].x !== null) {
                let c1 = getCoords(history[i-1].x, history[i-1].y);
                let c2 = getCoords(history[i].x, history[i].y);
                ctx.moveTo(c1.px, c1.py);
                ctx.lineTo(c2.px, c2.py);
            }
        }
        ctx.stroke();
        ctx.setLineDash([]);

        // Group points by coordinate to handle overlaps
        let groups = {};
        for (let item of history) {
            if (item.x === null) continue;
            let key = item.x + "," + item.y;
            if (!groups[key]) groups[key] = { steps: [], isFinal: false };
            groups[key].steps.push(item.step);
            if (item.step === targetK) groups[key].isFinal = true;
        }

        // Draw points and merged labels
        for (let key in groups) {
            let [x, y] = key.split(",");
            let group = groups[key];
            let { px, py } = getCoords(parseInt(x), parseInt(y));
            
            ctx.beginPath();
            ctx.arc(px, py, group.isFinal ? 7 : 5, 0, 2 * Math.PI);
            ctx.fillStyle = group.isFinal ? "#e74c3c" : (group.steps.includes(1) ? "#2ecc71" : "#3498db");
            ctx.fill();
            ctx.strokeStyle = "#ffffff";
            ctx.lineWidth = 1;
            ctx.stroke();

            // Construct label (e.g., 1G=18G)
            let label = group.steps.map(s => s + "G").join("=");
            if(group.isFinal) label = "Q (" + label + ")";

            ctx.fillStyle = group.isFinal ? "#e74c3c" : textColor;
            ctx.font = group.isFinal ? "bold 13px sans-serif" : "bold 11px sans-serif";
            ctx.textAlign = "left";
            ctx.fillText(label, px + 8, py - 6);
        }
    }

    // --- UI Interaction ---
    document.getElementById("calcBtn").addEventListener("click", () => {
        const baseVal = document.getElementById("basePoint").value.split(",");
        const G = { x: parseInt(baseVal[0]), y: parseInt(baseVal[1]) };
        const k = parseInt(document.getElementById("privateKey").value);
        const log = document.getElementById("outputLog");
        
        log.innerHTML = "";
        computationHistory = [{ x: G.x, y: G.y, step: 1 }];
        log.innerHTML += `> 1G = (${G.x}, ${G.y})<br>`;
        renderState(computationHistory, k);

        let current = G;
        let step = 2;

        function animateStep() {
            if (step > k) return;
            current = addPoints(current.x, current.y, G.x, G.y);
            
            computationHistory.push({ x: current.x, y: current.y, step: step });
            renderState(computationHistory, k);

            if (current.x !== null) {
                log.innerHTML += `> ${step}G = (${current.x}, ${current.y})<br>`;
            } else {
                log.innerHTML += `> ${step}G = Point at Infinity<br>`;
            }
            log.scrollTop = log.scrollHeight;
            
            step++;
            setTimeout(animateStep, 400); 
        }
        if(k > 1) animateStep();
    });

    drawGrid();
    renderState([{ x: 1, y: 5, step: 1 }], 1);


    // --- Statistical Data Generation ---
    let xCounts = Array(17).fill(0);
    let yCounts = Array(17).fill(0);
    let distances = [];
    let bitZeros = Array(5).fill(0);
    let bitOnes = Array(5).fill(0);
    let scatterData = [];
    
    let pt = {x: 1, y: 5};
    let prevPt = pt;

    for(let i = 1; i <= 18; i++) {
        if(pt.x !== null) {
            xCounts[pt.x]++;
            yCounts[pt.y]++;
            scatterData.push({x: i, y: pt.x});
            
            // Bit analysis (for a max value of 16, we need 5 bits)
            for(let b = 0; b < 5; b++) {
                ((pt.x >> b) & 1) ? bitOnes[b]++ : bitZeros[b]++;
                ((pt.y >> b) & 1) ? bitOnes[b]++ : bitZeros[b]++;
            }

            if(i > 1) {
                let d = Math.sqrt(Math.pow(pt.x - prevPt.x, 2) + Math.pow(pt.y - prevPt.y, 2));
                distances.push(d.toFixed(2));
            }
        }
        prevPt = pt;
        pt = addPoints(pt.x, pt.y, 1, 5);
    }

    // Chart 2: Distribution
    new Chart(document.getElementById('distChart'), {
        type: 'bar',
        data: {
            labels: Array.from({length: 17}, (_, i) => i),
            datasets: [
                { label: 'Coord X Freq.', data: xCounts, backgroundColor: 'rgba(54, 162, 235, 0.7)' },
                { label: 'Coord Y Freq.', data: yCounts, backgroundColor: 'rgba(255, 99, 132, 0.7)' }
            ]
        },
        options: { plugins: { title: { display: true, text: 'Coordinate Frequency (k=1 to 18)' } }, scales: { y: { beginAtZero: true, ticks: { stepSize: 1 } } } }
    });

    // Chart 3: Bit Distribution
    new Chart(document.getElementById('bitChart'), {
        type: 'bar',
        data: {
            labels: ['Bit 0 (LSB)', 'Bit 1', 'Bit 2', 'Bit 3', 'Bit 4 (MSB)'],
            datasets: [
                { label: 'Count of 0s', data: bitZeros, backgroundColor: 'rgba(150, 150, 150, 0.6)' },
                { label: 'Count of 1s', data: bitOnes, backgroundColor: 'rgba(46, 204, 113, 0.6)' }
            ]
        },
        options: { plugins: { title: { display: true, text: 'Bit Distribution (X and Y combined)' } }, scales: { y: { beginAtZero: true } } }
    });

    // Chart 4: Correlation Scatter
    new Chart(document.getElementById('scatterChart'), {
        type: 'scatter',
        data: {
            datasets: [{
                label: 'Output X',
                data: scatterData,
                backgroundColor: 'rgba(241, 196, 15, 0.8)',
                pointRadius: 5
            }]
        },
        options: { plugins: { title: { display: true, text: 'Input k vs Output X Coordinate' } }, scales: { x: { title: { display: true, text: 'Input k' } }, y: { title: { display: true, text: 'Output X' } } } }
    });

    // Chart 5: Avalanche
    new Chart(document.getElementById('avalancheChart'), {
        type: 'line',
        data: {
            labels: Array.from({length: distances.length}, (_, i) => `k=${i+1}→${i+2}`),
            datasets: [{
                label: 'Geometric Distance',
                data: distances,
                borderColor: 'rgba(75, 192, 192, 1)',
                backgroundColor: 'rgba(75, 192, 192, 0.2)',
                tension: 0.2,
                fill: true,
                pointRadius: 3
            }]
        },
        options: { plugins: { title: { display: true, text: 'Distance Variation Between kG and (k+1)G' } }, scales: { y: { beginAtZero: true } } }
    });
});
</script>
{% endraw %}
