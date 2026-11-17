---
layout: page
title: Statistical Independence & Contingency Analysis
description: Programmatic contingency table construction, marginal and conditional frequency profiling, Pearson's Chi-square test, and causal inference discussion.
img: assets/img/hmw6.jpg
importance: 6
category: Statistics
permalink: /projects/homework6/
---

This assignment investigates the mathematical and empirical foundations of **statistical independence** between two categorical or discretized random variables $$(X, F)$$. Starting from any bivariate dataset of paired observations $$\{(x_k, f_k)\}_{k=1}^N$$, we programmatically construct the two-way contingency table, derive marginal and conditional frequency distributions, evaluate expected counts under the null hypothesis of independence, and compute Pearson's $$\chi^2$$ test statistic alongside its exact $$p$$-value. Finally, we examine the epistemic boundary separating statistical dependence from causal mechanisms.

---

### 1. Theoretical Foundations of Statistical Independence

#### Probability Theory & Joint Distribution Factorization

In probability theory, two events $$A$$ and $$B$$ defined on a probability space $$(\Omega, \mathcal{F}, P)$$ are **statistically independent** (denoted $$A \perp\mkern-9mu\perp B$$) if and only if the probability of their joint occurrence equals the product of their individual probabilities:

$$
P(A \cap B) = P(A) \cdot P(B)
$$

Equivalently, whenever $$P(B) > 0$$, conditioning on $$B$$ provides no additional information about $$A$$:

$$
P(A \mid B) = \frac{P(A \cap B)}{P(B)} = P(A)
$$

Extending this principle to two discrete random variables $$X$$ and $$F$$ taking values in finite alphabets $$\mathcal{X} = \{x_1, \dots, x_r\}$$ and $$\mathcal{F} = \{f_1, \dots, f_c\}$$, the variables are **statistically independent** ($$X \perp\mkern-9mu\perp F$$) if and only if their joint Probability Mass Function (PMF) factorizes globally into the product of their marginal PMFs:

$$
P(X = x_i, F = f_j) = P(X = x_i) \cdot P(F = f_j) \qquad \forall \, i \in \{1, \dots, r\}, \; j \in \{1, \dots, c\}
$$

#### Empirical Contingency Tables: Marginal & Conditional Frequencies

Given a bivariate dataset $$\mathcal{D} = \{(x_k, f_k)\}_{k=1}^N$$ of size $$N$$, we organize the joint counts into an $$r \times c$$ **contingency table** where each cell $$O_{ij}$$ records the observed frequency of the pair $$(x_i, f_j)$$:

$$
O_{ij} = \sum_{k=1}^N \mathbf{1}_{\{x_k = x_i \;\wedge\; f_k = f_j\}}
$$

From the interior cell counts $$O_{ij}$$, we obtain the **row and column marginal totals** and their corresponding **empirical marginal frequencies**:

$$
R_i = \sum_{j=1}^c O_{ij}, \quad \hat{p}_{i\cdot} = \frac{R_i}{N} \qquad \text{and} \qquad C_j = \sum_{i=1}^r O_{ij}, \quad \hat{p}_{\cdot j} = \frac{C_j}{N}
$$

To inspect how the distribution of $$F$$ shifts across levels of $$X$$ (and vice versa), we compute the **row-conditional** and **column-conditional frequencies**:

$$
\hat{P}(F = f_j \mid X = x_i) = \frac{O_{ij}}{R_i}, \qquad \hat{P}(X = x_i \mid F = f_j) = \frac{O_{ij}}{C_j}
$$

Under exact independence, every row-conditional profile matches the marginal distribution of $$F$$, i.e., $$\frac{O_{ij}}{R_i} = \frac{C_j}{N}$$ for all $$i$$.

<div class="my-4 p-4 rounded z-depth-1" style="background-color: rgba(128,128,128,0.03); border: 1px solid rgba(150,150,150,0.25);" markdown="0">
    <div class="row align-items-stretch">
        <!-- INDEPENDENCE DIAGRAM -->
        <div class="col-md-6 mb-4 mb-md-0 d-flex flex-column justify-content-between border-right-md" style="border-color: rgba(150,150,150,0.2);">
            <div class="text-center mb-3">
                <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #2ecc71; font-size: 0.85rem; white-space: nowrap;">
                    INDEPENDENCE: X ⊥ F
                </span>
                <p class="small text-muted mt-2 mb-0">Identical Conditional Profiles: P(F | X = xᵢ) = P(F)</p>
            </div>
            <svg viewBox="0 0 360 205" class="w-100 mt-auto" style="max-height: 205px; display: block; overflow: visible;">
                <!-- Axes -->
                <line x1="50" y1="165" x2="330" y2="165" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <line x1="50" y1="165" x2="50" y2="20" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <text x="15" y="95" fill="currentColor" font-size="11" font-weight="bold" transform="rotate(-90 15,95)" text-anchor="middle">P(F | X)</text>
                <!-- Row 1 Stacked Bar (40% / 60%) -->
                <rect x="80" y="93" width="55" height="72" fill="#3498db" fill-opacity="0.8"/>
                <rect x="80" y="45" width="55" height="48" fill="#9b59b6" fill-opacity="0.8"/>
                <text x="107" y="133" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">60%</text>
                <text x="107" y="73" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">40%</text>
                <text x="107" y="184" fill="currentColor" font-size="11" text-anchor="middle">X = x₁</text>
                <!-- Row 2 Stacked Bar (40% / 60%) -->
                <rect x="165" y="93" width="55" height="72" fill="#3498db" fill-opacity="0.8"/>
                <rect x="165" y="45" width="55" height="48" fill="#9b59b6" fill-opacity="0.8"/>
                <text x="192" y="133" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">60%</text>
                <text x="192" y="73" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">40%</text>
                <text x="192" y="184" fill="currentColor" font-size="11" text-anchor="middle">X = x₂</text>
                <!-- Marginal Reference Bar (40% / 60%) -->
                <rect x="255" y="93" width="55" height="72" fill="#3498db" fill-opacity="0.35" stroke="#3498db" stroke-width="1.5" stroke-dasharray="4,2"/>
                <rect x="255" y="45" width="55" height="48" fill="#9b59b6" fill-opacity="0.35" stroke="#9b59b6" stroke-width="1.5" stroke-dasharray="4,2"/>
                <text x="282" y="184" fill="currentColor" font-size="11" font-weight="bold" text-anchor="middle">Marginal P(F)</text>
                <!-- Horizontal Reference Line -->
                <line x1="50" y1="93" x2="325" y2="93" stroke="#2ecc71" stroke-width="1.5" stroke-dasharray="5,3"/>
            </svg>
        </div>
        <!-- DEPENDENCE DIAGRAM -->
        <div class="col-md-6 d-flex flex-column justify-content-between pl-md-4">
            <div class="text-center mb-3">
                <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #e74c3c; font-size: 0.85rem; white-space: nowrap;">
                    DEPENDENCE: X ⊥̸ F
                </span>
                <p class="small text-muted mt-2 mb-0">Divergent Conditional Profiles: P(F | X = xᵢ) ≠ P(F)</p>
            </div>
            <svg viewBox="0 0 360 205" class="w-100 mt-auto" style="max-height: 205px; display: block; overflow: visible;">
                <!-- Axes -->
                <line x1="50" y1="165" x2="330" y2="165" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" /
                <line x1="50" y1="165" x2="50" y2="20" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <text x="15" y="95" fill="currentColor" font-size="11" font-weight="bold" transform="rotate(-90 15,95)" text-anchor="middle">P(F | X)</text>
                <!-- Row 1 Stacked Bar (35% / 65%) -->
                <rect x="80" y="123" width="55" height="42" fill="#3498db" fill-opacity="0.8"/>
                <rect x="80" y="45" width="55" height="78" fill="#9b59b6" fill-opacity="0.8"/>
                <text x="107" y="148" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">35%</text>
                <text x="107" y="88" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">65%</text>
                <text x="107" y="184" fill="currentColor" font-size="11" text-anchor="middle">X = x₁</text>
                <!-- Row 2 Stacked Bar (85% / 15%) -->
                <rect x="165" y="63" width="55" height="102" fill="#3498db" fill-opacity="0.8"/>
                <rect x="165" y="45" width="55" height="18" fill="#9b59b6" fill-opacity="0.8"/>
                <text x="192" y="118" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">85%</text>
                <text x="192" y="58" fill="#ffffff" font-size="10" font-weight="bold" text-anchor="middle">15%</text>
                <text x="192" y="184" fill="currentColor" font-size="11" text-anchor="middle">X = x₂</text>
                <!-- Marginal Reference Bar (40% / 60%) -->
                <rect x="255" y="93" width="55" height="72" fill="#3498db" fill-opacity="0.35" stroke="#3498db" stroke-width="1.5" stroke-dasharray="4,2"/>
                <rect x="255" y="45" width="55" height="48" fill="#9b59b6" fill-opacity="0.35" stroke="#9b59b6" stroke-width="1.5" stroke-dasharray="4,2"/>
                <text x="282" y="184" fill="currentColor" font-size="11" font-weight="bold" text-anchor="middle">Marginal P(F)</text>
                <!-- Horizontal Reference Line -->
                <line x1="50" y1="93" x2="325" y2="93" stroke="#e74c3c" stroke-width="1.5" stroke-dasharray="5,3"/>
            </svg>
        </div>
    </div>
</div>

#### Expected Frequencies & Pearson's $$\chi^2$$ Test of Independence

To test the null hypothesis $$H_0: X \perp\mkern-9mu\perp F$$ against the alternative $$H_1: X \not\perp F$$ (where $$\perp\mkern-9mu\perp$$ denotes statistical independence and $$\not\perp$$ denotes statistical dependence), we compute the **expected frequency** $$E_{ij}$$ in every cell $$(i, j)$$ under the assumption that the joint probabilities factorize via the observed marginals:

$$
E_{ij} = N \cdot \hat{p}_{i\cdot} \cdot \hat{p}_{\cdot j} = N \left(\frac{R_i}{N}\right)\left(\frac{C_j}{N}\right) = \frac{R_i \cdot C_j}{N}
$$

**Pearson's Chi-Square statistic** aggregates the normalized squared deviations between observed and expected cell frequencies across the entire contingency table:

$$
\chi^2 = \sum_{i=1}^r \sum_{j=1}^c \frac{(O_{ij} - E_{ij})^2}{E_{ij}}
$$

By Pearson's theorem, when $$H_0$$ holds and sample size $$N$$ is sufficiently large (standard Cochran rule: $$E_{ij} \ge 5$$), the test statistic converges in distribution to a $$\chi^2_{\nu}$$ random variable with **degrees of freedom**:

$$
\nu = (r - 1)(c - 1)
$$

The corresponding **$$p$$-value** is the upper-tail probability evaluated via the regularized upper incomplete gamma function $$Q(a, x)$$:

$$
p\text{-value} = P\left(\chi^2_{\nu} \ge \chi^2_{\text{obs}}\right) = Q\left(\frac{\nu}{2}, \, \frac{\chi^2_{\text{obs}}}{2}\right) = 1 - \frac{1}{\Gamma(\nu/2)} \int_0^{\chi^2_{\text{obs}}/2} t^{\nu/2 - 1} e^{-t} \, dt
$$

Because $$\chi^2$$ scales linearly with sample size $$N$$ for fixed table proportions, we also report **Cramér's $$V$$** to measure the normalized strength of association on $$[0, 1]$$:

$$
V = \sqrt{\frac{\chi^2}{N \cdot \min(r - 1, \, c - 1)}}
$$

---

### 2. Interactive Bivariate Contingency & Independence Engine

This engine programmatically ingests any bivariate dataset $$(X, F)$$—either generated from configurable security/statistical scenarios or entered directly as custom `(x, f)` pairs—constructs the contingency matrix, derives marginal and conditional distributions, and runs the $$\chi^2$$ independence test in real time.

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);" markdown="0">
    <h5 class="text-center mb-4 font-weight-bold">Bivariate Contingency Table & Chi-Square Suite</h5>
<style>
    .ind-row { display: flex; flex-wrap: wrap; align-items: center; justify-content: center; gap: 0.75rem 1.25rem; margin-bottom: 1rem; }
    .ind-row label { margin: 0; }
    .ind-select { flex: 1 1 280px; max-width: 520px; min-width: 0; background-color: rgba(128,128,128,0.05); color: inherit; }
    .ind-slider { flex: 1 1 220px; max-width: 340px; text-align: center; }
    .ind-slider input[type=range] { width: 100%; }
    .ind-charts { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1.5rem; }
    .ind-chart-box { position: relative; width: 100%; height: 290px; }
</style>
<!-- Dataset Controls (3 structured rows) -->
<div class="px-4 mb-3">
    <!-- ROW 1: label + dataset selector -->
    <div class="ind-row">
        <label for="indDatasetMode" class="font-weight-bold" style="white-space: nowrap;">Bivariate Dataset Source (X, F):</label>
        <select id="indDatasetMode" class="form-control ind-select" onchange="onDatasetModeChange()">
            <option value="cyber_dep">Scenario 1: Attack Vector (X) vs. Breach Outcome (F) [Dependent]</option>
            <option value="net_indep">Scenario 2: Network Protocol (X) vs. Packet Drop (F) [Independent]</option>
            <option value="auth_mod">Scenario 3: Auth Mechanism (X) vs. Account Takeover (F) [Moderate]</option>
            <option value="custom">Custom Bivariate Dataset: Raw (X, F) Pairs Input</option>
        </select>
    </div>
    <!-- ROW 2: Sample Size (N) and Association Strength (λ) on the same row -->
    <div class="ind-row" id="indSlidersRow">
        <div class="ind-slider">
            <label class="font-weight-bold d-block">Sample Size (N): <span id="indNVal" style="color: #3498db;">400</span></label>
            <input type="range" id="indNRange" class="custom-range" min="50" max="2000" step="50" value="400"
                   oninput="document.getElementById('indNVal').innerText = this.value; runIndependenceEngine();">
        </div>
        <div class="ind-slider">
            <label class="font-weight-bold d-block">Association Strength (λ): <span id="indLambdaVal" style="color: #9b59b6;">0.80</span></label>
            <input type="range" id="indLambdaRange" class="custom-range" min="0.00" max="1.00" step="0.05" value="0.80"
                   oninput="document.getElementById('indLambdaVal').innerText = parseFloat(this.value).toFixed(2); runIndependenceEngine();">
        </div>
    </div>
    <!-- Custom dataset input (shown only when 'custom' is selected) -->
    <div id="indCustomBox" class="mb-3 mx-auto text-center" style="display: none; max-width: 650px;">
        <label class="font-weight-bold d-block text-center">Enter Bivariate Pairs <code>X, F</code> (one observation per line):</label>
        <textarea id="indCustomInput" rows="5" class="form-control font-monospace text-center" style="background-color: rgba(128,128,128,0.05); color: inherit; font-size: 0.85rem;" oninput="runIndependenceEngine()">Phishing, Compromised
Phishing, Compromised
Phishing, Blocked
Malware, Compromised
Malware, Compromised
Malware, Compromised
Malware, Blocked
BruteForce, Blocked
BruteForce, Blocked
BruteForce, Blocked
BruteForce, Compromised
Phishing, Compromised
BruteForce, Blocked
Malware, Compromised
Phishing, Blocked
BruteForce, Blocked
Malware, Compromised
Phishing, Compromised</textarea>
        <small class="text-muted d-block mt-1">Any number of distinct categories for X and F is automatically detected and tabulated.</small>
    </div>
    <!-- ROW 3: resample button -->
    <div class="ind-row" id="indResampleBtnRow">
        <button type="button" class="btn btn-sm btn-outline-primary px-4" onclick="runIndependenceEngine()">Sample New Bivariate Dataset (X, F)</button>
    </div>
</div>
    <!-- Summary Hypothesis Test Results Table -->
    <div class="px-4 mb-4">
        <div class="table-responsive">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th class="text-center">Sample Size (N)</th>
                        <th class="text-center">Table Shape (r × c)</th>
                        <th class="text-center">Degrees of Freedom (ν)</th>
                        <th class="text-center">Pearson χ² Statistic</th>
                        <th class="text-center">p-value</th>
                        <th class="text-center">Cramér's V</th>
                        <th class="text-center">Statistical Decision (α = 0.05)</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td id="statN" class="font-weight-bold text-center">-</td>
                        <td id="statDim" class="text-center">-</td>
                        <td id="statDf" class="text-center">-</td>
                        <td id="statChi2" class="text-center" style="color: #3498db; font-weight: bold;">-</td>
                        <td id="statPVal" class="text-center" style="color: #9b59b6; font-weight: bold;">-</td>
                        <td id="statCramer" class="text-center">-</td>
                        <td id="statDecision" class="font-weight-bold text-center">-</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    <hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">
    <!-- Programmatically Constructed Tables -->
    <div class="px-4 mb-4">
        <div class="row justify-content-center">
            <!-- Observed & Marginals -->
            <div class="col-lg-6 mb-4 text-center">
                <h6 class="font-weight-bold text-center mb-2">1. Observed Contingency Table (Oᵢⱼ) & Marginals</h6>
                <div class="table-responsive" id="obsTableContainer"></div>
            </div>
            <!-- Expected & Chi2 Contributions -->
            <div class="col-lg-6 mb-4 text-center">
                <h6 class="font-weight-bold text-center mb-2">2. Expected Frequencies (Eᵢⱼ) & Cell χ² Terms</h6>
                <div class="table-responsive" id="expTableContainer"></div>
            </div>
        </div>
        <!-- Conditional Frequencies Table -->
        <div class="row justify-content-center mt-2">
            <div class="col-lg-10 text-center">
                <h6 class="font-weight-bold text-center mb-2">3. Conditional Frequency Profiles P(F = fⱼ | X = xᵢ) vs. Marginal P(F = fⱼ)</h6>
                <div class="table-responsive" id="condTableContainer"></div>
            </div>
        </div>
    </div>
    <hr class="w-75 mx-auto mb-4" style="border-color: rgba(150,150,150,0.2);">
    <!-- Visual Charts -->
    <!-- Visual Charts -->
<div class="px-4">
    <div class="ind-charts">
        <div>
            <h6 class="font-weight-bold text-center mb-3">Conditional Distributions P(F | X = xᵢ) vs. Marginal P(F)</h6>
            <div class="ind-chart-box"><canvas id="indCondChart"></canvas></div>
        </div>
        <div>
            <h6 class="font-weight-bold text-center mb-3">Sampling Distribution χ²(ν) & Observed Test Statistic</h6>
            <div class="ind-chart-box"><canvas id="indChi2Chart"></canvas></div>
        </div>
    </div>
</div>
    <!-- Dynamic Interpretation Callout -->
    <div class="px-4 mt-2">
        <div id="indInterpretationBox" class="p-3 rounded text-center" style="background-color: rgba(128,128,128,0.07); border-top: 3px solid #3498db;">
            Initializing statistical contingency engine...
        </div>
    </div>
</div>

---

### 3. Analytical Conclusions: Association vs. Causality

#### 1. Interpreting the Contingency Table and $$\chi^2$$ Test

* **Evidence Against Independence:** When $$\chi^2$$ exceeds the critical threshold $$\chi^2_{1-\alpha, \nu}$$ (equivalently, $$p\text{-value} < \alpha$$), the observed conditional distributions $$\hat{P}(F \mid X = x_i)$$ deviate significantly from the marginal profile $$\hat{P}(F)$$. Knowing the state of $$X$$ shifts the probability distribution of $$F$$, meaning the variables carry mutual information $$I(X; F) > 0$$.
* **Failure to Reject $$H_0$$:** When $$p\text{-value} \ge \alpha$$, the discrepancies $$(O_{ij} - E_{ij})$$ are consistent with random sampling fluctuations under the product measure $$P(X, F) = P(X)P(F)$$. Note that failing to reject $$H_0$$ does not prove exact mathematical independence ($$I(X; F) = 0$$), particularly at small sample sizes $$N$$ where statistical power is low.
* **Sample Size Sensitivity:** Because $$\chi^2 = N \sum_{i,j} \frac{(\hat{p}_{ij} - \hat{p}_{i\cdot}\hat{p}_{\cdot j})^2}{\hat{p}_{i\cdot}\hat{p}_{\cdot j}}$$, arbitrarily large datasets ($$N \to \infty$$) will produce tiny $$p$$-values for negligible real-world deviations. Evaluating **Cramér's $$V$$** alongside the $$p$$-value separates statistical significance from practical effect magnitude.

#### 2. Why Statistical Dependence Does Not Imply Causality

Establishing $$X \not\perp F$$ is strictly a statement about the **joint observational distribution** $$P(X, F)$$. Crucially, statistical dependence is **symmetric** ($$X \not\perp F \iff F \not\perp X$$), whereas causal influence is **asymmetric** ($$X \to F$$ differs fundamentally from $$F \to X$$). In Judea Pearl's causal hierarchy, observational conditioning $$P(F \mid X = x)$$ differs from interventional distributions $$P(F \mid \text{do}(X = x))$$ due to three primary structural mechanisms:

| Non-Causal Mechanism | Causal Graph (DAG) | Statistical Mechanism |
| :--- | :--- | :--- |
| **Confounding (Common Cause)** | $$X \leftarrow Z \rightarrow F$$ | A latent confounder $$Z$$ drives both $$X$$ and $$F$$. Conditioning on $$Z$$ restores conditional independence ($$X \perp\mkern-9mu\perp F \mid Z$$), whereas marginalizing over $$Z$$ induces spurious association (and can even reverse trends, known as **Simpson's Paradox**). |
| **Reverse Causality** | $$F \rightarrow X$$ | The contingency table detects strong dependence, but $$F$$ is the physical cause of $$X$$ rather than its consequence. |
| **Selection / Collider Bias** | $$X \rightarrow S \leftarrow F$$ | Two independent variables $$X \perp\mkern-9mu\perp F$$ share a common effect $$S$$. Restricting data collection to a subset of $$S$$ (e.g., analyzing only systems that triggered an audit log) induces artificial dependence between $$X$$ and $$F$$. |

Consequently, while a contingency table and $$\chi^2$$ test rigorously identify **predictive associations** across variables, establishing **causality** requires randomized experimental assignment (which severs incoming arrows into $$X$$) or valid causal identification strategies (such as back-door adjustment over measured confounders).

<!-- Scripts for interactive contingency & independence suite -->
{% raw %}
<script>
// Load Chart.js v4 dynamically and run the callback once it is available
function loadChartJs(callback) {
    if (window.Chart && Chart.version && parseInt(Chart.version, 10) >= 3) { callback(); return; }
    var s = document.createElement("script");
    s.src = "https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js";
    s.onload = callback;
    s.onerror = function () { console.error("Chart.js failed to load"); };
    document.head.appendChild(s);
}
</script>
<script>
let indCondChartInstance = null;
let indChi2ChartInstance = null;

// --- NUMERICAL SPECIAL FUNCTIONS FOR CHI-SQUARE P-VALUE ---

function logGamma(z) {
    const c = [
        76.18009172947146, -86.50532032941677, 24.01409824083091,
        -1.231739572450155, 0.1208650973866179e-2, -0.5395239384953e-5
    ];
    let x = z;
    let y = z;
    let tmp = x + 5.5;
    tmp -= (x + 0.5) * Math.log(tmp);
    let ser = 1.000000000190015;
    for (let j = 0; j < 6; j++) {
        y += 1;
        ser += c[j] / y;
    }
    return -tmp + Math.log(2.5066282746310005 * ser / x);
}

// Regularized upper incomplete gamma function Q(a, x) = 1 - P(a, x)
function gammaQ(a, x) {
    if (x <= 0.0) return 1.0;
    if (a <= 0.0) return 0.0;

    if (x < a + 1.0) {
        let ap = a;
        let sum = 1.0 / a;
        let del = sum;
        for (let n = 1; n <= 300; n++) {
            ap += 1.0;
            del *= x / ap;
            sum += del;
            if (Math.abs(del) < Math.abs(sum) * 1e-14) break;
        }
        let pVal = sum * Math.exp(-x + a * Math.log(x) - logGamma(a));
        return Math.max(0.0, Math.min(1.0, 1.0 - pVal));
    } else {
        let b = x + 1.0 - a;
        let c = 1.0 / 1e-30;
        let d = 1.0 / b;
        let h = d;
        for (let i = 1; i <= 300; i++) {
            let an = -i * (i - a);
            b += 2.0;
            d = an * d + b;
            if (Math.abs(d) < 1e-30) d = 1e-30;
            c = b + an / c;
            if (Math.abs(c) < 1e-30) c = 1e-30;
            d = 1.0 / d;
            let del = d * c;
            h *= del;
            if (Math.abs(del - 1.0) < 1e-14) break;
        }
        let qVal = Math.exp(-x + a * Math.log(x) - logGamma(a)) * h;
        return Math.max(0.0, Math.min(1.0, qVal));
    }
}

function chiSquarePValue(chi2, df) {
    if (df <= 0 || chi2 <= 0) return 1.0;
    return gammaQ(df / 2.0, chi2 / 2.0);
}

function chiSquarePdf(x, df) {
    if (x <= 0 || df <= 0) return 0.0;
    let k2 = df / 2.0;
    let logVal = (k2 - 1.0) * Math.log(x) - (x / 2.0) - k2 * Math.log(2.0) - logGamma(k2);
    return Math.exp(logVal);
}

function chiSquareCritical(alpha, df) {
    let low = 0.0;
    let high = Math.max(20.0, df * 5.0);
    while (chiSquarePValue(high, df) > alpha) {
        high *= 2.0;
    }
    for (let iter = 0; iter < 50; iter++) {
        let mid = 0.5 * (low + high);
        if (chiSquarePValue(mid, df) > alpha) {
            low = mid;
        } else {
            high = mid;
        }
    }
    return 0.5 * (low + high);
}

// --- BIVARIATE DATASET GENERATION & PARSING ---

function sampleCategorical(labels, probs) {
    let u = Math.random();
    let cum = 0.0;
    for (let i = 0; i < labels.length; i++) {
        cum += probs[i];
        if (u <= cum) return labels[i];
    }
    return labels[labels.length - 1];
}

function generateBivariateDataset(mode, N, lambda) {
    let pairs = [];
    if (mode === "cyber_dep") {
        const xCats = ["Phishing", "Malware", "BruteForce"];
        const xProbs = [0.40, 0.35, 0.25];
        const fCats = ["Compromised", "Blocked", "Quarantined"];
        const fMarg = [0.35, 0.45, 0.20];
        const fCondMap = {
            "Phishing":    [0.65, 0.20, 0.15],
            "Malware":     [0.45, 0.25, 0.30],
            "BruteForce":  [0.10, 0.80, 0.10]
        };
        for (let i = 0; i < N; i++) {
            let x = sampleCategorical(xCats, xProbs);
            let cond = fCondMap[x];
            let mixed = cond.map((p, idx) => lambda * p + (1.0 - lambda) * fMarg[idx]);
            let f = sampleCategorical(fCats, mixed);
            pairs.push([x, f]);
        }
    } else if (mode === "net_indep") {
        const xCats = ["TCP", "UDP", "ICMP"];
        const xProbs = [0.55, 0.30, 0.15];
        const fCats = ["Delivered", "Dropped"];
        const fMarg = [0.82, 0.18];
        const fCondMap = {
            "TCP":  [0.82, 0.18],
            "UDP":  [0.82, 0.18],
            "ICMP": [0.82, 0.18]
        };
        for (let i = 0; i < N; i++) {
            let x = sampleCategorical(xCats, xProbs);
            let cond = fCondMap[x];
            let mixed = cond.map((p, idx) => lambda * p + (1.0 - lambda) * fMarg[idx]);
            let f = sampleCategorical(fCats, mixed);
            pairs.push([x, f]);
        }
    } else if (mode === "auth_mod") {
        const xCats = ["PasswordOnly", "SMS_2FA", "HardwareKey"];
        const xProbs = [0.35, 0.40, 0.25];
        const fCats = ["Takeover", "Prevented"];
        const fMarg = [0.22, 0.78];
        const fCondMap = {
            "PasswordOnly": [0.42, 0.58],
            "SMS_2FA":      [0.16, 0.84],
            "HardwareKey":  [0.02, 0.98]
        };
        for (let i = 0; i < N; i++) {
            let x = sampleCategorical(xCats, xProbs);
            let cond = fCondMap[x];
            let mixed = cond.map((p, idx) => lambda * p + (1.0 - lambda) * fMarg[idx]);
            let f = sampleCategorical(fCats, mixed);
            pairs.push([x, f]);
        }
    }
    return pairs;
}

function parseCustomBivariateDataset(rawText) {
    let lines = rawText.split(/\r?\n/);
    let pairs = [];
    for (let line of lines) {
        let trimmed = line.trim();
        if (!trimmed) continue;
        let parts = trimmed.split(/[,;\t]+/);
        if (parts.length >= 2) {
            let xVal = parts[0].trim();
            let fVal = parts[1].trim();
            if (xVal && fVal) {
                pairs.push([xVal, fVal]);
            }
        }
    }
    return pairs;
}

function onDatasetModeChange() {
    let modeEl = document.getElementById("indDatasetMode");
    if (!modeEl) return;
    let mode = modeEl.value;
    let isCustom = (mode === "custom");

    document.getElementById("indCustomBox").style.display = isCustom ? "block" : "none";
    document.getElementById("indSlidersRow").style.display = isCustom ? "none" : "flex";
    document.getElementById("indResampleBtnRow").style.display = isCustom ? "none" : "flex";

    if (mode === "net_indep") {
        document.getElementById("indLambdaRange").value = "0.00";
        document.getElementById("indLambdaVal").innerText = "0.00";
    } else if (mode === "cyber_dep") {
        document.getElementById("indLambdaRange").value = "0.80";
        document.getElementById("indLambdaVal").innerText = "0.80";
    } else if (mode === "auth_mod") {
        document.getElementById("indLambdaRange").value = "0.75";
        document.getElementById("indLambdaVal").innerText = "0.75";
    }

    runIndependenceEngine();
}

// --- PROGRAMMATIC CONTINGENCY CONSTRUCTION & INFERENCE ---

function runIndependenceEngine() {
    let modeEl = document.getElementById("indDatasetMode");
    if (!modeEl) return;
    let mode = modeEl.value;
    let pairs = [];

    if (mode === "custom") {
        pairs = parseCustomBivariateDataset(document.getElementById("indCustomInput").value);
    } else {
        let N = parseInt(document.getElementById("indNRange").value, 10);
        let lambda = parseFloat(document.getElementById("indLambdaRange").value);
        pairs = generateBivariateDataset(mode, N, lambda);
    }

    if (pairs.length === 0) return;

    // 1. Extract ordered unique categories for X (rows) and F (columns)
    let xSet = [];
    let fSet = [];
    pairs.forEach(([x, f]) => {
        if (!xSet.includes(x)) xSet.push(x);
        if (!fSet.includes(f)) fSet.push(f);
    });

    let r = xSet.length;
    let c = fSet.length;
    let N = pairs.length;

    // 2. Construct Observed Matrix O[i][j]
    let obs = Array.from({ length: r }, () => new Array(c).fill(0));
    pairs.forEach(([x, f]) => {
        let i = xSet.indexOf(x);
        let j = fSet.indexOf(f);
        obs[i][j]++;
    });

    // 3. Compute Row Marginals R[i] and Column Marginals C[j]
    let rowTotals = obs.map(row => row.reduce((acc, v) => acc + v, 0));
    let colTotals = new Array(c).fill(0);
    for (let j = 0; j < c; j++) {
        for (let i = 0; i < r; i++) {
            colTotals[j] += obs[i][j];
        }
    }

    // 4. Compute Expected Frequencies E[i][j], Chi-Square Terms, and Conditional Frequencies
    let exp = Array.from({ length: r }, () => new Array(c).fill(0));
    let chiTerms = Array.from({ length: r }, () => new Array(c).fill(0));
    let condFgivenX = Array.from({ length: r }, () => new Array(c).fill(0));

    let chi2Total = 0.0;
    for (let i = 0; i < r; i++) {
        for (let j = 0; j < c; j++) {
            let eVal = (rowTotals[i] * colTotals[j]) / N;
            exp[i][j] = eVal;
            let term = eVal > 0 ? Math.pow(obs[i][j] - eVal, 2) / eVal : 0.0;
            chiTerms[i][j] = term;
            chi2Total += term;
            condFgivenX[i][j] = rowTotals[i] > 0 ? obs[i][j] / rowTotals[i] : 0.0;
        }
    }

    let df = Math.max(1, (r - 1) * (c - 1));
    let pVal = (r > 1 && c > 1) ? chiSquarePValue(chi2Total, df) : 1.0;
    let minDim = Math.min(r - 1, c - 1);
    let cramerV = (minDim > 0 && N > 0) ? Math.sqrt(chi2Total / (N * minDim)) : 0.0;

    // 5. Update Summary Metrics Table
    document.getElementById("statN").innerText = N;
    document.getElementById("statDim").innerText = `${r} × ${c}`;
    document.getElementById("statDf").innerText = (r > 1 && c > 1) ? df : "N/A (<2 cats)";
    document.getElementById("statChi2").innerText = chi2Total.toFixed(4);
    document.getElementById("statPVal").innerText = pVal < 1e-6 ? pVal.toExponential(3) : pVal.toFixed(6);
    document.getElementById("statCramer").innerText = cramerV.toFixed(4);

    let decCell = document.getElementById("statDecision");
    if (r < 2 || c < 2) {
        decCell.innerText = "Need ≥ 2 categories per variable";
        decCell.style.color = "#f39c12";
    } else if (pVal < 0.05) {
        decCell.innerText = "Dependent (Reject H₀)";
        decCell.style.color = "#e74c3c";
    } else {
        decCell.innerText = "Independent (Fail to Reject H₀)";
        decCell.style.color = "#2ecc71";
    }

    // 6. Render HTML Tables, Charts & Interpretation
    renderContingencyTables(xSet, fSet, obs, exp, chiTerms, condFgivenX, rowTotals, colTotals, N);
function renderIndependenceCharts(xSet, fSet, condFgivenX, colTotals, N, chi2Obs, df) {
    if (typeof Chart === "undefined") return;

    const palette = ["#3498db", "#9b59b6", "#2ecc71", "#e67e22", "#e74c3c", "#1abc9c"];

    // --- Chart 1: conditional distributions P(F | X = x_i) vs. marginal P(F) ---
    let labelsWithMarginal = [...xSet.map(x => `X = ${x}`), "Marginal P(F)"];
    let datasets = fSet.map((fName, j) => {
        let dataArr = xSet.map((_, i) => parseFloat((condFgivenX[i][j] * 100).toFixed(2)));
        dataArr.push(parseFloat(((colTotals[j] / N) * 100).toFixed(2)));
        let color = palette[j % palette.length];
        return { label: `F = ${fName}`, data: dataArr, backgroundColor: color, borderColor: color, borderWidth: 1 };
    });

    if (indCondChartInstance) indCondChartInstance.destroy();
    indCondChartInstance = new Chart(document.getElementById("indCondChart"), {
        type: "bar",
        data: { labels: labelsWithMarginal, datasets: datasets },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            animation: false,
            scales: {
                x: { title: { display: true, text: "Conditioning Variable (X) vs. Marginal" } },
                y: { title: { display: true, text: "Conditional Frequency (%)" }, min: 0, max: 100 }
            }
        }
    });

    // --- Chart 2: chi-square density, rejection region, observed statistic ---
    let critVal = chiSquareCritical(0.05, df);
    let maxX = Math.max(critVal * 1.6, chi2Obs * 1.25, df + 4 * Math.sqrt(2 * df));
    let steps = 200;
    let dx = maxX / steps;

    let pdfData = [];
    let tailData = [];
    let peakPdf = 0;
    for (let k = 1; k <= steps; k++) {
        let x = k * dx;
        let y = chiSquarePdf(x, df);
        if (!isFinite(y)) continue;
        pdfData.push({ x: x, y: y });
        if (x >= critVal) tailData.push({ x: x, y: y });
        if (y > peakPdf) peakPdf = y;
    }
    let yTop = peakPdf * 1.1;

    if (indChi2ChartInstance) indChi2ChartInstance.destroy();
    indChi2ChartInstance = new Chart(document.getElementById("indChi2Chart"), {
        type: "line",
        data: {
            datasets: [
                {
                    label: `χ²(ν=${df}) Density`,
                    data: pdfData,
                    borderColor: "#3498db",
                    borderWidth: 2,
                    pointRadius: 0,
                    fill: false
                },
                {
                    label: `Rejection Region (α=0.05, crit=${critVal.toFixed(2)})`,
                    data: tailData,
                    borderColor: "rgba(231, 76, 60, 0.9)",
                    backgroundColor: "rgba(231, 76, 60, 0.25)",
                    borderWidth: 1,
                    pointRadius: 0,
                    fill: "origin"
                },
                {
                    label: `Observed χ² = ${chi2Obs.toFixed(2)}`,
                    data: [{ x: chi2Obs, y: 0 }, { x: chi2Obs, y: yTop }],
                    borderColor: "#9b59b6",
                    borderWidth: 3,
                    borderDash: [6, 4],
                    pointRadius: 0,
                    fill: false
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            animation: false,
            parsing: false,
            scales: {
                x: { type: "linear", min: 0, max: maxX, title: { display: true, text: "Chi-Square Value (x)" } },
                y: { min: 0, max: yTop, title: { display: true, text: "Probability Density f(x)" } }
            }
        }
    });
}
    updateInterpretationText(r, c, N, chi2Total, df, pVal, cramerV);
}

function renderContingencyTables(xSet, fSet, obs, exp, chiTerms, condFgivenX, rowTotals, colTotals, N) {
    // Table 1: Observed Counts + Marginals
    let h1 = `<table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3); font-size: 0.85rem;">`;
    h1 += `<thead style="background-color: rgba(128,128,128,0.1);"><tr><th class="text-center">X \\ F</th>`;
    fSet.forEach(f => h1 += `<th class="text-center">${f}</th>`);
    h1 += `<th class="text-center" style="color: #3498db;">Row Total (Rᵢ)</th><th class="text-center">Marginal P(X)</th></tr></thead><tbody>`;

    for (let i = 0; i < xSet.length; i++) {
        h1 += `<tr><td class="font-weight-bold text-center">${xSet[i]}</td>`;
        for (let j = 0; j < fSet.length; j++) {
            h1 += `<td class="text-center">${obs[i][j]}</td>`;
        }
        h1 += `<td class="font-weight-bold text-center" style="color: #3498db;">${rowTotals[i]}</td>`;
        h1 += `<td class="text-center">${(rowTotals[i] / N).toFixed(3)}</td></tr>`;
    }
    h1 += `<tr style="background-color: rgba(128,128,128,0.06);"><td class="font-weight-bold text-center" style="color: #9b59b6;">Col Total (Cⱼ)</td>`;
    colTotals.forEach(cTot => h1 += `<td class="font-weight-bold text-center" style="color: #9b59b6;">${cTot}</td>`);
    h1 += `<td class="font-weight-bold text-center">${N}</td><td class="text-center">1.000</td></tr>`;

    h1 += `<tr style="background-color: rgba(128,128,128,0.06);"><td class="font-weight-bold text-center">Marginal P(F)</td>`;
    colTotals.forEach(cTot => h1 += `<td class="text-center">${(cTot / N).toFixed(3)}</td>`);
    h1 += `<td class="text-center">1.000</td><td class="text-center">—</td></tr></tbody></table>`;
    document.getElementById("obsTableContainer").innerHTML = h1;

    // Table 2: Expected Frequencies + Cell Chi2 Contributions
    let h2 = `<table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3); font-size: 0.85rem;">`;
    h2 += `<thead style="background-color: rgba(128,128,128,0.1);"><tr><th class="text-center">X \\ F</th>`;
    fSet.forEach(f => h2 += `<th class="text-center">${f}</th>`);
    h2 += `<th class="text-center">Row χ²</th></tr></thead><tbody>`;

    for (let i = 0; i < xSet.length; i++) {
        h2 += `<tr><td class="font-weight-bold text-center">${xSet[i]}</td>`;
        let rowChi = 0;
        for (let j = 0; j < fSet.length; j++) {
            rowChi += chiTerms[i][j];
            h2 += `<td class="text-center">${exp[i][j].toFixed(2)} <span class="text-muted small">(+${chiTerms[i][j].toFixed(2)})</span></td>`;
        }
        h2 += `<td class="font-weight-bold text-center" style="color: #e67e22;">${rowChi.toFixed(3)}</td></tr>`;
    }
    h2 += `</tbody></table>`;
    document.getElementById("expTableContainer").innerHTML = h2;

    // Table 3: Conditional Frequency Profiles P(F | X) vs Marginal P(F)
    let h3 = `<table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3); font-size: 0.85rem;">`;
    h3 += `<thead style="background-color: rgba(128,128,128,0.1);"><tr><th class="text-center">Condition X = xᵢ</th>`;
    fSet.forEach(f => h3 += `<th class="text-center">P(F = ${f} | X)</th>`);
    h3 += `<th class="text-center">Row Sum</th></tr></thead><tbody>`;

    for (let i = 0; i < xSet.length; i++) {
        h3 += `<tr><td class="font-weight-bold text-center">X = ${xSet[i]}</td>`;
        for (let j = 0; j < fSet.length; j++) {
            let marg = colTotals[j] / N;
            let diff = Math.abs(condFgivenX[i][j] - marg);
            let highlight = diff > 0.12 ? 'style="color: #e74c3c; font-weight: bold;"' : '';
            h3 += `<td class="text-center" ${highlight}>${(condFgivenX[i][j] * 100).toFixed(1)}%</td>`;
        }
        h3 += `<td class="text-center">100.0%</td></tr>`;
    }
    h3 += `<tr style="background-color: rgba(52, 152, 219, 0.1);"><td class="font-weight-bold text-center" style="color: #3498db;">Unconditional Marginal P(F)</td>`;
    colTotals.forEach(cTot => h3 += `<td class="font-weight-bold text-center" style="color: #3498db;">${((cTot / N) * 100).toFixed(1)}%</td>`);
    h3 += `<td class="font-weight-bold text-center">100.0%</td></tr></tbody></table>`;
    document.getElementById("condTableContainer").innerHTML = h3;
}

function renderIndependenceCharts(xSet, fSet, condFgivenX, colTotals, N, chi2Obs, df) {
    if (typeof Chart === "undefined") return;

    const palette = ["#3498db", "#9b59b6", "#2ecc71", "#e67e22", "#e74c3c", "#1abc9c"];

    // Chart 1: Grouped Bar Chart of Conditional Distributions P(F | X = x_i) + Marginal P(F)
    let labelsWithMarginal = [...xSet.map(x => `X = ${x}`), "Marginal P(F)"];
    let datasets = fSet.map((fName, j) => {
        let dataArr = xSet.map((_, i) => parseFloat((condFgivenX[i][j] * 100).toFixed(2)));
        dataArr.push(parseFloat(((colTotals[j] / N) * 100).toFixed(2)));
        let color = palette[j % palette.length];
        return {
            label: `F = ${fName}`,
            data: dataArr,
            backgroundColor: color,
            borderColor: color,
            borderWidth: 1
        };
    });

    const ctxCond = document.getElementById("indCondChart").getContext("2d");
    if (indCondChartInstance) indCondChartInstance.destroy();

    indCondChartInstance = new Chart(ctxCond, {
        type: "bar",
        data: {
            labels: labelsWithMarginal,
            datasets: datasets
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: "Conditioning Variable (X) vs. Marginal" } },
                y: { title: { display: true, text: "Conditional Frequency (%)" }, min: 0, max: 100 }
            }
        }
    });

    // Chart 2: Chi-Square PDF & Critical Cutoff / Observed Statistic
    let critVal = chiSquareCritical(0.05, df);
    let maxX = Math.max(critVal * 1.6, chi2Obs * 1.25, df + 4 * Math.sqrt(2 * df));
    let steps = 120;
    let dx = maxX / steps;

    let xGrid = [];
    let pdfData = [];
    let tailData = [];
    let obsMarkerData = [];

    let peakPdf = 0;
    for (let k = 1; k <= steps; k++) {
        let x = k * dx;
        let y = chiSquarePdf(x, df);
        if (isFinite(y) && y > peakPdf) peakPdf = y;
    }

    for (let k = 1; k <= steps; k++) {
        let x = k * dx;
        let y = chiSquarePdf(x, df);
        xGrid.push(x.toFixed(1));
        pdfData.push(y);
        tailData.push(x >= critVal ? y : null);
        obsMarkerData.push(Math.abs(x - chi2Obs) <= dx * 0.6 ? peakPdf * 0.95 : null);
    }

    const ctxChi2 = document.getElementById("indChi2Chart").getContext("2d");
    if (indChi2ChartInstance) indChi2ChartInstance.destroy();

    indChi2ChartInstance = new Chart(ctxChi2, {
        type: "line",
        data: {
            labels: xGrid,
            datasets: [
                {
                    label: `χ²(ν=${df}) Density`,
                    data: pdfData,
                    borderColor: "#3498db",
                    borderWidth: 2,
                    pointRadius: 0,
                    fill: false
                },
                {
                    label: `Rejection Region (α=0.05, crit=${critVal.toFixed(2)})`,
                    data: tailData,
                    backgroundColor: "rgba(231, 76, 60, 0.25)",
                    borderColor: "transparent",
                    pointRadius: 0,
                    fill: true
                },
                {
                    label: `Observed χ² = ${chi2Obs.toFixed(2)}`,
                    data: obsMarkerData,
                    type: "bar",
                    backgroundColor: "#9b59b6",
                    barThickness: 4
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: "Chi-Square Value (x)" } },
                y: { title: { display: true, text: "Probability Density f(x)" }, beginAtZero: true }
            }
        }
    });
}

function updateInterpretationText(r, c, N, chi2, df, pVal, cramerV) {
    const box = document.getElementById("indInterpretationBox");
    if (!box) return;
    if (r < 2 || c < 2) {
        box.style.borderTopColor = "#f39c12";
        box.innerHTML = `<strong>Insufficient Dimensionality:</strong> At least two distinct values are required for both <em>X</em> and <em>F</em> to construct a valid contingency test (current shape: ${r} × ${c}).`;
        return;
    }

    let strengthLabel = cramerV < 0.10 ? "negligible" : (cramerV < 0.30 ? "weak-to-moderate" : (cramerV < 0.50 ? "moderate-to-strong" : "strong"));

    if (pVal < 0.05) {
        box.style.borderTopColor = "#e74c3c";
        box.innerHTML = `<strong>Statistical Dependence Detected (Reject H₀):</strong> With <em>N</em> = ${N} observations and <em>ν</em> = ${df} degrees of freedom, Pearson's statistic is <strong>χ² = ${chi2.toFixed(3)}</strong> (<em>p</em> = ${pVal < 1e-4 ? pVal.toExponential(3) : pVal.toFixed(4)} &lt; 0.05). The conditional distributions <em>P(F | X)</em> differ significantly from the marginal distribution <em>P(F)</em>, with a <strong>${strengthLabel}</strong> association (Cramér's <em>V</em> = ${cramerV.toFixed(3)}). However, this purely observational dependence does not by itself prove that <em>X</em> causes <em>F</em> without ruling out confounders or selection bias.`;
    } else {
        box.style.borderTopColor = "#2ecc71";
        box.innerHTML = `<strong>Consistent with Statistical Independence (Fail to Reject H₀):</strong> With <em>N</em> = ${N} observations and <em>ν</em> = ${df} degrees of freedom, Pearson's statistic is <strong>χ² = ${chi2.toFixed(3)}</strong> (<em>p</em> = ${pVal.toFixed(4)} ≥ 0.05). Observed deviations between <em>Oᵢⱼ</em> and expected counts <em>Eᵢⱼ = (Rᵢ·Cⱼ)/N</em> are consistent with random sampling variation, and conditional profiles <em>P(F | X = xᵢ)</em> closely track the unconditional marginal distribution <em>P(F)</em> (Cramér's <em>V</em> = ${cramerV.toFixed(3)}).`;
    }
}

function tryInitHomework6() {
    if (typeof Chart === "undefined" || !document.getElementById("indCondChart")) {
        setTimeout(tryInitHomework6, 50);
        return;
    }
    onDatasetModeChange();
}

if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", tryInitHomework6);
} else {
    tryInitHomework6();
}
</script>
{% endraw %}
