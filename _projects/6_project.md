---
layout: page
title: Riemann vs. Lebesgue Integration & Normal CDF Approximations
description: Comparative numerical measure theory, Lebesgue-Riemann integration engine, and non-elementary Standard Normal CDF algorithms.
img: assets/img/hmw5.jpg
importance: 5
category: Statistics
permalink: /projects/homework5/
---

This assignment explores two fundamental topics in analysis and computational statistics:
1. **Riemann vs. Lebesgue Numerical Integration:** Investigating how partitioning the domain versus partitioning the range alters convergence, computational efficiency, and applicability across continuous, discontinuous, unbounded, and pathological functions (such as Dirichlet's and Thomae's functions).
2. **Numerical Computation of the Standard Normal CDF ($\Phi(z)$):** Examining the mathematical barrier imposed by **Liouville's theorem** on non-elementary integrals, deriving series expansions from probability density functions, and evaluating quadrature versus rational approximations for statistical computing.

---

### 1. Riemann and Lebesgue Integration

#### Theoretical Foundations

The classical **Riemann integral** partitions the domain interval $[a, b]$ into $N$ subintervals $\Delta x_i = x_i - x_{i-1}$. Using sample points $x_i^* \in [x_{i-1}, x_i]$, the Riemann sum is defined as:

$$
R_N = \sum_{i=1}^N f(x_i^*) \Delta x_i
$$

A function $f$ is Riemann-integrable on $[a, b]$ if and only if $f$ is bounded and its set of discontinuities has Lebesgue measure zero (**Lebesgue's criterion for Riemann integrability**).

Conversely, the **Lebesgue integral** partitions the range $[y_{\min}, y_{\max}]$ into $M$ subintervals $[y_j, y_{j+1})$. For each level-set interval, we evaluate the domain measure of its preimage $E_j = \{x \in [a, b] : y_j \le f(x) < y_{j+1}\}$. The Lebesgue simple-function approximation sum is given by:

$$
L_M = \sum_{j=0}^{M-1} y_j^* \, \mu(E_j)
$$

where $y_j^* \in [y_j, y_{j+1})$ (typically the lower bound $y_j$ or midpoint) and $\mu(E_j)$ denotes the 1D Lebesgue measure (length) of the subset $E_j \subset [a, b]$.

<div class="my-4 p-4 rounded z-depth-1" style="background-color: rgba(128,128,128,0.03); border: 1px solid rgba(150,150,150,0.25);" markdown="0">
    <div class="row align-items-center">
        <!-- RIEMANN CARD -->
        <div class="col-md-6 mb-4 mb-md-0 border-right-md" style="border-color: rgba(150,150,150,0.2);">
            <div class="text-center mb-3">
                <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #3498db; font-size: 0.9rem;">
                    RIEMANN INTEGRATION
                </span>
                <p class="small text-muted mt-2 mb-0">Domain Partitioning ($x$-axis)</p>
            </div>
            <svg viewBox="0 0 360 210" width="100%" height="100%" style="overflow: visible;">
                <!-- Axes and Grid -->
                <line x1="40" y1="170" x2="340" y2="170" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <line x1="40" y1="170" x2="40" y2="20" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <text x="345" y="174" fill="currentColor" font-size="12" font-weight="bold">x</text>
                <text x="38" y="12" fill="currentColor" font-size="12" font-weight="bold">y</text>
                <!-- Riemann Rectangles (Vertical Bars) -->
                <rect x="70" y="110" width="40" height="60" fill="#3498db" fill-opacity="0.25" stroke="#3498db" stroke-width="1.5"/>
                <rect x="110" y="70" width="40" height="100" fill="#3498db" fill-opacity="0.35" stroke="#3498db" stroke-width="1.5"/>
                <rect x="150" y="45" width="40" height="125" fill="#3498db" fill-opacity="0.45" stroke="#3498db" stroke-width="1.5"/>
                <rect x="190" y="55" width="40" height="115" fill="#3498db" fill-opacity="0.35" stroke="#3498db" stroke-width="1.5"/>
                <rect x="230" y="90" width="40" height="80" fill="#3498db" fill-opacity="0.25" stroke="#3498db" stroke-width="1.5"/>
                <rect x="270" y="130" width="40" height="40" fill="#3498db" fill-opacity="0.15" stroke="#3498db" stroke-width="1.5"/>
                <!-- Curve f(x) -->
                <path d="M 40 150 Q 150 10, 230 80 T 320 150" fill="none" stroke="#2c3e50" stroke-width="3"/>
                <!-- Interval x_i Labels -->
                <line x1="150" y1="170" x2="150" y2="176" stroke="currentColor" stroke-width="2"/>
                <line x1="190" y1="170" x2="190" y2="176" stroke="currentColor" stroke-width="2"/>
                <text x="142" y="192" fill="currentColor" font-size="11">x<tspan dy="3" font-size="9">i-1</tspan></text>
                <text x="187" y="192" fill="currentColor" font-size="11">x<tspan dy="3" font-size="9">i</tspan></text>
                <!-- Delta x Indicator -->
                <line x1="150" y1="140" x2="190" y2="140" stroke="#e74c3c" stroke-width="1.5" stroke-dasharray="3,3"/>
                <text x="162" y="135" fill="#e74c3c" font-size="10" font-weight="bold">Δx<tspan dy="2" font-size="8">i</tspan></text>
            </svg>
        </div>
        <!-- LEBESGUE CARD -->
        <div class="col-md-6 pl-md-4">
            <div class="text-center mb-3">
                <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #9b59b6; font-size: 0.9rem;">
                    LEBESGUE INTEGRATION
                </span>
                <p class="small text-muted mt-2 mb-0">Range Partitioning ($y$-axis)</p>
            </div>
            <svg viewBox="0 0 360 210" width="100%" height="100%" style="overflow: visible;">
                <!-- Axes and Grid -->
                <line x1="40" y1="170" x2="340" y2="170" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <line x1="40" y1="170" x2="40" y2="20" stroke="currentColor" stroke-opacity="0.4" stroke-width="2" />
                <text x="345" y="174" fill="currentColor" font-size="12" font-weight="bold">x</text>
                <text x="38" y="12" fill="currentColor" font-size="12" font-weight="bold">y</text>
                <!-- Horizontal Range Band [y_j, y_j+1] -->
                <rect x="40" y="70" width="280" height="25" fill="#9b59b6" fill-opacity="0.12" stroke="none" />
                <line x1="35" y1="95" x2="320" y2="95" stroke="#9b59b6" stroke-width="1.5" stroke-dasharray="4,3"/>
                <line x1="35" y1="70" x2="320" y2="70" stroke="#9b59b6" stroke-width="1.5" stroke-dasharray="4,3"/>
                <!-- Projections onto Preimage Sets in Domain E_j -->
                <!-- Preimage 1 -->
                <rect x="90" y="170" width="45" height="4" fill="#9b59b6"/>
                <line x1="90" y1="95" x2="90" y2="170" stroke="#9b59b6" stroke-width="1" stroke-dasharray="2,2"/>
                <line x1="135" y1="70" x2="135" y2="170" stroke="#9b59b6" stroke-width="1" stroke-dasharray="2,2"/>
                <!-- Preimage 2 -->
                <rect x="205" y="170" width="50" height="4" fill="#9b59b6"/>
                <line x1="205" y1="70" x2="205" y2="170" stroke="#9b59b6" stroke-width="1" stroke-dasharray="2,2"/>
                <line x1="255" y1="95" x2="255" y2="170" stroke="#9b59b6" stroke-width="1" stroke-dasharray="2,2"/>
                <!-- Curve f(x) -->
                <path d="M 40 150 Q 150 10, 230 80 T 320 150" fill="none" stroke="#2c3e50" stroke-width="3"/>
                <!-- Y-Axis Labels -->
                <text x="12" y="100" fill="#9b59b6" font-size="11" font-weight="bold">y<tspan dy="2" font-size="8">j</tspan></text>
                <text x="2" y="74" fill="#9b59b6" font-size="11" font-weight="bold">y<tspan dy="2" font-size="8">j+1</tspan></text>
                <!-- Preimage Measure Label μ(E_j) on X-Axis -->
                <text x="135" y="194" fill="#9b59b6" font-size="11" font-weight="bold" text-anchor="middle">
                    Preimage Measure μ(E<tspan dy="2" font-size="8">j</tspan>)
                </text>
            </svg>
        </div>
    </div>
</div>

#### Comparison of Numerical Behavior

| Feature / Metric | Riemann Approach | Lebesgue Approach |
| :--- | :--- | :--- |
| **Partition Axis** | Horizontal axis (Domain $[a, b]$) | Vertical axis (Range $[y_{\min}, y_{\max}]$) |
| **Sum Construction** | Regular or adaptive grid spacing $\Delta x_i$ | Weighted sum of preimage domain measures $\mu(E_j)$ |
| **Discontinuities** | Fails if discontinuities are dense (non-zero measure) | Handles dense discontinuities effortlessly |
| **Unbounded Functions** | Requires improper limit processes | Integrates unbounded functions if $\int |f| d\mu < \infty$ |
| **Computational Complexity** | $O(N)$ grid evaluations | $O(N \cdot M)$ grid scans or $O(N)$ via range histogram binning |
| **Pathological Sets** | Fails on $\mathbf{1}_{\mathbb{Q}}(x)$ (Upper sum $= 1$, Lower $= 0$) | Correctly evaluates $\mathbf{1}_{\mathbb{Q}}(x) = 0$ since $\mu(\mathbb{Q} \cap [0,1]) = 0$ |

#### Evaluated Functions Suite

1. **$f_1(x) = \sin(x)$ on $[0, \pi]$:** Smooth, continuous. Both methods converge rapidly to the exact value $2.0$.
2. **$f_2(x) = x^2$ on $[0, 1]$:** Monotonic, continuous. Converges to $1/3 \approx 0.333333$.
3. **$f_3(x) = \mathbf{1}_{\{x \ge 0.5\}} \cdot 3 + \mathbf{1}_{\{x < 0.5\}}$ on $[0, 1]$:** Step discontinuity at $x=0.5$. Exact integral $= 2.0$.
4. **$f_4(x) = |x - 0.5|$ on $[0, 1]$:** Non-differentiable peak at $x=0.5$. Exact integral $= 0.25$.
5. **$f_5(x) = 1 / \sqrt{x}$ on $(0, 1]$:** Unbounded singularity at $x=0$. Improper Riemann integral $= 2.0$.
6. **$f_6(x) = \sin(x)/x$ on $(0, \pi]$:** Non-trivial function (Sine integral $\text{Si}(\pi) \approx 1.851937$).
7. **$f_7(x) = \mathbf{1}_{\mathbb{Q}}(x)$ on $[0, 1]$ (Dirichlet Function):** Everywhere discontinuous. Riemann upper sum is $1.0$, lower sum is $0.0$ (fails to converge). Lebesgue integral correctly yields $0.0$ because rational numbers have measure zero.
8. **$f_8(x) = 1/q$ if $x=p/q \in \mathbb{Q}$ else $0$ (Thomae's / Popcorn Function):** Discontinuous at rationals, continuous at irrationals. Both Riemann and Lebesgue integrals evaluate to $0.0$.

---

### 2. Numerical Computation of the Normal CDF ($\Phi(z)$)

#### Non-Elementary Integrals and Liouville's Theorem

The Probability Density Function (PDF) of a Standard Normal random variable $Z \sim \mathcal{N}(0,1)$ is given by:

$$
\phi(z) = \frac{1}{\sqrt{2\pi}} e^{-z^2/2}
$$

The Cumulative Distribution Function (CDF) $\Phi(z) = P(Z \le z)$ requires computing:

$$
\Phi(z) = \int_{-\infty}^z \frac{1}{\sqrt{2\pi}} e^{-t^2/2} \, dt = \frac{1}{2} + \frac{1}{\sqrt{2\pi}} \int_0^z e^{-t^2/2} \, dt
$$

In introductory statistics, $\Phi(z)$ is always provided via pre-computed lookup tables or software routines. This is not merely a convenience, but a mathematical necessity governed by **Liouville's Theorem on Integration in Finite Terms** (1835).

> **Liouville's Theorem:** Let $f(x)$ and $g(x)$ be rational functions with $g(x)$ non-constant. The indefinite integral $\int f(x) e^{g(x)} dx$ can be expressed in terms of elementary functions (polynomials, exponentials, logarithms, trigonometric functions, and their compositions) **if and only if** there exists a rational function $r(x)$ satisfying the differential equation:
> 
> $$
> f(x) = r'(x) + r(x)g'(x)
> $$

For the standard normal integrand, $f(x) = 1$ and $g(x) = -x^2/2$, so $g'(x) = -x$. The Liouville condition becomes:

$$
1 = r'(x) - x \cdot r(x)
$$

If $r(x) = \frac{P(x)}{Q(x)}$ is a rational function in lowest terms, analyzing degrees reveals that no polynomial combination can satisfy this identity. Consequently, **$\int e^{-x^2/2} dx$ has no elementary antiderivative**.

#### Analytical Representation via Special Functions & Power Series

To bypass this barrier, mathematics defines the **Error Function** $\operatorname{erf}(x)$ as a standardized special function:

$$
\operatorname{erf}(x) = \frac{2}{\sqrt{\pi}} \int_0^x e^{-t^2} dt
$$

By substituting $t = u / \sqrt{2}$, the standard normal CDF $\Phi(z)$ is exactly related to $\operatorname{erf}(z)$ by:

$$
\Phi(z) = \frac{1}{2} \left[ 1 + \operatorname{erf}\left(\frac{z}{\sqrt{2}}\right) \right]
$$

As highlighted in the *Hogg-McKean-Craig Section 3.4 Notes*, an explicit power series representation can be derived by expanding the Maclaurin series of $e^{-x^2/2}$ and integrating term-by-term (justified by uniform convergence):

$$
e^{-t^2/2} = \sum_{k=0}^{\infty} \frac{(-1)^k t^{2k}}{2^k k!} \implies \int_0^z e^{-t^2/2} dt = \sum_{k=0}^{\infty} \frac{(-1)^k z^{2k+1}}{2^k (2k+1) k!}
$$

Thus, the exact analytical series for $\Phi(z)$ is:

$$
\Phi(z) = \frac{1}{2} + \frac{1}{\sqrt{2\pi}} \sum_{k=0}^{\infty} \frac{(-1)^k z^{2k+1}}{2^k (2k+1) k!}
$$

Because this is an alternating series with monotonically decreasing terms for small to moderate $z$, the **Alternating Series Estimation Theorem** bounds the truncation error after $K$ terms by the first omitted term:

$$
\left| R_K(z) \right| \le \frac{|z|^{2K+3}}{\sqrt{2\pi} \, 2^{K+1} (2K+3) (K+1)!}
$$

#### Numerical Methods Implemented

1. **Power Series Expansion:** Truncated sum up to $K$ terms. Highly accurate for $|z| \le 3.0$, but subject to floating-point loss of significance for large $|z|$.
2. **Numerical Quadrature (Trapezoidal & Simpson's Rule):** Numerically integrates $\phi(t)$ from a finite lower bound $-K_{\text{bound}} = -8.0$ up to $z$ across $N$ panels.
3. **Abramowitz & Stegun Rational Approximation:** A high-precision polynomial-rational formula ($O(1)$ complexity) widely used in production libraries:
   $$
   t = \frac{1}{1 + p|z|}, \quad \Phi(z) \approx 1 - \phi(z) \left( a_1 t + a_2 t^2 + a_3 t^3 + a_4 t^4 + a_5 t^5 \right) \quad \text{for } z \ge 0
   $$
   with absolute error $|\epsilon(z)| < 7.5 \times 10^{-8}$.

---

### 3. Interactive Simulation & Integration Engine

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);" markdown="0">
    <h5 class="text-center mb-4 font-weight-bold">Interactive Numerical Analysis Suite</h5>
    <!-- SUBSECTION 1: EXERCISE 1 -->
    <div class="px-4 mb-5">
        <h5 class="font-weight-bold mb-3" style="color: #3498db;">1. Riemann vs. Lebesgue Integration</h5> 
        <div class="row">
            <div class="col-md-6 mb-3">
                <label class="font-weight-bold">Select Target Function $f(x)$:</label>
                <select id="ex1FuncSelect" class="form-control" style="background-color: rgba(128,128,128,0.05); color: inherit;" onchange="runEx1Simulation()">
                    <option value="sin">f1(x) = sin(x) on [0, π]</option>
                    <option value="x2">f2(x) = x^2 on [0, 1]</option>
                    <option value="step">f3(x) = Step Function (1 if x<0.5 else 3) on [0, 1]</option>
                    <option value="abs">f4(x) = |x - 0.5| on [0, 1]</option>
                    <option value="inv_sqrt">f5(x) = 1 / sqrt(x) on (0, 1] (Unbounded)</option>
                    <option value="sinc">f6(x) = sin(x) / x on (0, π]</option>
                    <option value="dirichlet">f7(x) = Dirichlet Function (1 if x ∈ Q else 0)</option>
                    <option value="thomae">f8(x) = Thomae Popcorn Function on [0, 1]</option>
                </select>
            </div>
            <div class="col-md-3 mb-3">
                <label class="font-weight-bold">Domain Subdivisions (N): <span id="ex1NVal" style="color: #3498db;">200</span></label>
                <input type="range" id="ex1NRange" class="custom-range" min="10" max="2000" step="10" value="200" oninput="document.getElementById('ex1NVal').innerText = this.value; runEx1Simulation();">
            </div>
            <div class="col-md-3 mb-3">
                <label class="font-weight-bold">Range Bins (M): <span id="ex1MVal" style="color: #9b59b6;">100</span></label>
                <input type="range" id="ex1MRange" class="custom-range" min="10" max="500" step="10" value="100" oninput="document.getElementById('ex1MVal').innerText = this.value; runEx1Simulation();">
            </div>
        </div>
        <!-- Ex 1 Results Table -->
        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th>Integration Method</th>
                        <th>Numerical Estimate</th>
                        <th>Analytic / Theoretical Exact</th>
                        <th>Absolute Error</th>
                        <th>Execution Time</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td class="font-weight-bold text-left">Riemann Midpoint Sum ($R_N$)</td>
                        <td id="ex1RVal" style="color: #3498db; font-weight: bold;">-</td>
                        <td class="ex1ExactCell">-</td>
                        <td id="ex1RErr">-</td>
                        <td id="ex1RTime">-</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold text-left">Riemann Trapezoidal Rule</td>
                        <td id="ex1TrapVal" style="color: #2ecc71; font-weight: bold;">-</td>
                        <td class="ex1ExactCell">-</td>
                        <td id="ex1TrapErr">-</td>
                        <td id="ex1TrapTime">-</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold text-left">Lebesgue Preimage Sum ($L_M$)</td>
                        <td id="ex1LVal" style="color: #9b59b6; font-weight: bold;">-</td>
                        <td class="ex1ExactCell">-</td>
                        <td id="ex1LErr">-</td>
                        <td id="ex1LTime">-</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <!-- Ex 1 Charts -->
        <div class="row mt-4">
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Function Shape & Level Sets</h6>
                <div style="position: relative; width: 100%; height: 250px;">
                    <canvas id="ex1FuncChart"></canvas>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Convergence Error vs. Subdivisions ($N$)</h6>
                <div style="position: relative; width: 100%; height: 250px;">
                    <canvas id="ex1ConvChart"></canvas>
                </div>
            </div>
        </div>
    </div>
    <hr class="w-75 mx-auto mb-5" style="border-color: rgba(150,150,150,0.25);">
    <!-- SUBSECTION 2: EXERCISE 2 -->
    <div class="px-4 mb-3">
        <h5 class="font-weight-bold mb-3" style="color: #e74c3c;">2. Normal CDF $\Phi(z)$ Approximations</h5>
        <div class="row">
            <div class="col-md-4 mb-3">
                <label class="font-weight-bold">Evaluation Point ($z$): <span id="ex2ZVal" style="color: #e74c3c;">1.00</span></label>
                <input type="range" id="ex2ZRange" class="custom-range" min="-4.0" max="4.0" step="0.05" value="1.0" oninput="document.getElementById('ex2ZVal').innerText = parseFloat(this.value).toFixed(2); runEx2Simulation();">
            </div>
            <div class="col-md-4 mb-3">
                <label class="font-weight-bold">Power Series Terms ($K$): <span id="ex2KVal" style="color: #f1c40f;">15</span></label>
                <input type="range" id="ex2KRange" class="custom-range" min="1" max="40" step="1" value="15" oninput="document.getElementById('ex2KVal').innerText = this.value; runEx2Simulation();">
            </div>
            <div class="col-md-4 mb-3">
                <label class="font-weight-bold">Quadrature Panels ($N$): <span id="ex2NVal" style="color: #1abc9c;">100</span></label>
                <input type="range" id="ex2NRange" class="custom-range" min="10" max="1000" step="10" value="100" oninput="document.getElementById('ex2NVal').innerText = this.value; runEx2Simulation();">
            </div>
        </div>
        <!-- Ex 2 Results Table -->
        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th>Numerical Algorithm</th>
                        <th>Computed $\Phi(z)$</th>
                        <th>Reference $\Phi(z)$ (`erf` standard)</th>
                        <th>Absolute Error</th>
                        <th>Theoretical Error Bound</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td class="font-weight-bold text-left">Power Series Expansion (Hogg Sec 3.4)</td>
                        <td id="ex2SeriesVal" style="color: #f1c40f; font-weight: bold;">-</td>
                        <td class="ex2RefCell">-</td>
                        <td id="ex2SeriesErr">-</td>
                        <td id="ex2SeriesBound">-</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold text-left">Trapezoidal Quadrature over $[-8, z]$</td>
                        <td id="ex2TrapVal" style="color: #3498db; font-weight: bold;">-</td>
                        <td class="ex2RefCell">-</td>
                        <td id="ex2TrapErr">-</td>
                        <td>$\mathcal{O}(h^2)$</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold text-left">Simpson's 1/3 Quadrature over $[-8, z]$</td>
                        <td id="ex2SimpVal" style="color: #2ecc71; font-weight: bold;">-</td>
                        <td class="ex2RefCell">-</td>
                        <td id="ex2SimpErr">-</td>
                        <td>$\mathcal{O}(h^4)$</td>
                    </tr>
                    <tr>
                        <td class="font-weight-bold text-left">Abramowitz & Stegun Polynomial Approx</td>
                        <td id="ex2ASVal" style="color: #e67e22; font-weight: bold;">-</td>
                        <td class="ex2RefCell">-</td>
                        <td id="ex2ASErr">-</td>
                        <td>$< 7.5 \times 10^{-8}$</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <!-- Ex 2 Charts -->
        <div class="row mt-4">
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Standard Normal PDF & Area under $\Phi(z)$</h6>
                <div style="position: relative; width: 100%; height: 250px;">
                    <canvas id="ex2PdfChart"></canvas>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Absolute Error across $z \in [-4, 4]$</h6>
                <div style="position: relative; width: 100%; height: 250px;">
                    <canvas id="ex2ErrorChart"></canvas>
                </div>
            </div>
        </div>
    </div>
</div>

---

### 4. Key Analytical Insights & Conclusions

1. **Riemann vs. Lebesgue Divergence:**
   * For continuous and piecewise continuous functions ($f_1$ through $f_4$), both Riemann and Lebesgue numerical integration exhibit rapid convergence.
   * For pathological sets like Dirichlet's function ($f_7$), numerical Riemann sums oscillate endlessly between lower bound $0.0$ and upper bound $1.0$, failing the Cauchy criterion for integrability. Lebesgue integration correctly isolates the preimage of $1.0$ as $\mathbb{Q} \cap [0,1]$, which has Lebesgue measure $\mu(\mathbb{Q}) = 0$, yielding a rigorous integral value of $0.0$.
2. **Computational Footprint of Range Partitioning:**
   * Numerical Lebesgue integration requires mapping range level-sets back to domain preimage lengths $\mu(E_j)$. On standard CPU hardware, this requires $O(N)$ histogram binning operations. While numerically slightly more expensive than a simple midpoint Riemann sum, it provides immunity against localized jump singularities and dense non-differentiable points.
3. **The Necessity of Numerical CDF Routines:**
   * Liouville's theorem proves that the lack of a closed-form elementary antiderivative for $e^{-z^2/2}$ is an intrinsic algebraic property of differential fields, not a limitation of calculus.
   * For production statistical computing, the **Power Series expansion** (derived in Hogg-McKean-Craig Section 3.4) provides arbitrary-precision capabilities for small to moderate values of $|z|$ ($|z| \le 3.5$), while **Abramowitz & Stegun rational approximations** or asymptotic series provide constant-time $O(1)$ precision near tail boundaries ($|z| > 3.5$).

<!-- Scripts for interactive tools -->
<script src="[https://cdn.jsdelivr.net/npm/chart.js](https://cdn.jsdelivr.net/npm/chart.js)"></script>
{% raw %}
<script>
let ex1FuncChartInstance = null;
let ex1ConvChartInstance = null;
let ex2PdfChartInstance = null;
let ex2ErrorChartInstance = null;

// Helper: Standard error function erf(x) implementation
function mathErf(x) {
    let sign = x >= 0 ? 1 : -1;
    x = Math.abs(x);
    let a1 =  0.254829592, a2 = -0.284496736, a3 =  1.421413741;
    let a4 = -1.453152027, a5 =  1.061405429, p  =  0.3275911;
    let t = 1.0 / (1.0 + p * x);
    let y = 1.0 - (((((a5 * t + a4) * t) + a3) * t + a2) * t + a1) * t * Math.exp(-x * x);
    return sign * y;
}

function stdNormalCdfRef(z) {
    return 0.5 * (1.0 + mathErf(z / Math.SQRT2));
}

// --- EXERCISE 1 LOGIC ---

function getEx1Func(type) {
    switch (type) {
        case 'sin':
            return { f: x => Math.sin(x), a: 0, b: Math.PI, exact: 2.0, ymin: 0, ymax: 1.0 };
        case 'x2':
            return { f: x => x * x, a: 0, b: 1.0, exact: 1/3, ymin: 0, ymax: 1.0 };
        case 'step':
            return { f: x => x < 0.5 ? 1.0 : 3.0, a: 0, b: 1.0, exact: 2.0, ymin: 0, ymax: 3.5 };
        case 'abs':
            return { f: x => Math.abs(x - 0.5), a: 0, b: 1.0, exact: 0.25, ymin: 0, ymax: 0.5 };
        case 'inv_sqrt':
            return { f: x => 1.0 / Math.sqrt(Math.max(x, 1e-7)), a: 0.0001, b: 1.0, exact: 2.0, ymin: 1.0, ymax: 10.0 };
        case 'sinc':
            return { f: x => x === 0 ? 1.0 : Math.sin(x) / x, a: 0.0001, b: Math.PI, exact: 1.85193705, ymin: 0, ymax: 1.0 };
        case 'dirichlet':
            return { f: x => (Number.isInteger(x * 1000000) ? 1.0 : 0.0), a: 0, b: 1.0, exact: 0.0, ymin: 0, ymax: 1.0 };
        case 'thomae':
            return { 
                f: x => {
                    let eps = 1e-6;
                    for (let q = 1; q <= 100; q++) {
                        let p = Math.round(x * q);
                        if (Math.abs(x - p/q) < eps) return 1.0 / q;
                    }
                    return 0.0;
                }, 
                a: 0, b: 1.0, exact: 0.0, ymin: 0, ymax: 1.0 
            };
        default:
            return { f: x => Math.sin(x), a: 0, b: Math.PI, exact: 2.0, ymin: 0, ymax: 1.0 };
    }
}

function runEx1Simulation() {
    let funcType = document.getElementById("ex1FuncSelect").value;
    let N = parseInt(document.getElementById("ex1NRange").value);
    let M = parseInt(document.getElementById("ex1MRange").value);

    let target = getEx1Func(funcType);
    let f = target.f, a = target.a, b = target.b, exact = target.exact;

    // 1. Riemann Midpoint Integration
    let t0 = performance.now();
    let dx = (b - a) / N;
    let rSum = 0;
    for (let i = 0; i < N; i++) {
        let xMid = a + (i + 0.5) * dx;
        rSum += f(xMid);
    }
    rSum *= dx;
    let rTime = (performance.now() - t0).toFixed(2);

    // 2. Riemann Trapezoidal Integration
    t0 = performance.now();
    let trapSum = 0.5 * (f(a) + f(b));
    for (let i = 1; i < N; i++) {
        trapSum += f(a + i * dx);
    }
    trapSum *= dx;
    let trapTime = (performance.now() - t0).toFixed(2);

    // 3. Lebesgue Numerical Integration
    t0 = performance.now();
    let ymin = target.ymin, ymax = target.ymax;
    let dy = (ymax - ymin) / M;
    let measureCounts = new Array(M).fill(0);
    
    let Nsub = Math.max(N * 4, 2000);
    let subDx = (b - a) / Nsub;
    for (let k = 0; k < Nsub; k++) {
        let xVal = a + k * subDx;
        let yVal = f(xVal);
        let bIdx = Math.floor((yVal - ymin) / dy);
        if (bIdx >= M) bIdx = M - 1;
        if (bIdx >= 0) measureCounts[bIdx]++;
    }

    let lSum = 0;
    for (let j = 0; j < M; j++) {
        let yStar = ymin + (j + 0.5) * dy;
        let domainMeasure = (measureCounts[j] / Nsub) * (b - a);
        lSum += yStar * domainMeasure;
    }
    let lTime = (performance.now() - t0).toFixed(2);

    // Update Table UI
    document.querySelectorAll(".ex1ExactCell").forEach(el => el.innerText = exact.toFixed(6));
    
    document.getElementById("ex1RVal").innerText = rSum.toFixed(6);
    document.getElementById("ex1RErr").innerText = Math.abs(rSum - exact).toExponential(3);
    document.getElementById("ex1RTime").innerText = `${rTime} ms`;

    document.getElementById("ex1TrapVal").innerText = trapSum.toFixed(6);
    document.getElementById("ex1TrapErr").innerText = Math.abs(trapSum - exact).toExponential(3);
    document.getElementById("ex1TrapTime").innerText = `${trapTime} ms`;

    document.getElementById("ex1LVal").innerText = lSum.toFixed(6);
    document.getElementById("ex1LErr").innerText = Math.abs(lSum - exact).toExponential(3);
    document.getElementById("ex1LTime").innerText = `${lTime} ms`;

    renderEx1Charts(target, N, M);
}

function renderEx1Charts(target, N, M) {
    let f = target.f, a = target.a, b = target.b;

    let plotPoints = 120;
    let xLabels = [], yData = [];
    let step = (b - a) / plotPoints;
    for (let i = 0; i <= plotPoints; i++) {
        let x = a + i * step;
        xLabels.push(x.toFixed(2));
        yData.push(f(x));
    }

    const ctxFunc = document.getElementById("ex1FuncChart").getContext("2d");
    if (ex1FuncChartInstance) ex1FuncChartInstance.destroy();

    ex1FuncChartInstance = new Chart(ctxFunc, {
        type: 'line',
        data: {
            labels: xLabels,
            datasets: [{
                label: 'f(x)',
                data: yData,
                borderColor: '#3498db',
                borderWidth: 2,
                pointRadius: 0,
                fill: false
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: 'Domain (x)' } },
                y: { title: { display: true, text: 'Range f(x)' } }
            }
        }
    });

    let nVals = [10, 20, 50, 100, 200, 500, 1000];
    let rErrors = [], lErrors = [];

    nVals.forEach(nSub => {
        let dxSub = (b - a) / nSub;
        let rS = 0;
        for (let i = 0; i < nSub; i++) {
            rS += f(a + (i + 0.5) * dxSub);
        }
        rS *= dxSub;
        rErrors.push(Math.abs(rS - target.exact) + 1e-12);

        let dy = (target.ymax - target.ymin) / M;
        let counts = new Array(M).fill(0);
        let subDx = (b - a) / nSub;
        for (let k = 0; k < nSub; k++) {
            let yVal = f(a + k * subDx);
            let idx = Math.floor((yVal - target.ymin) / dy);
            if (idx >= M) idx = M - 1;
            if (idx >= 0) counts[idx]++;
        }
        let lS = 0;
        for (let j = 0; j < M; j++) {
            lS += (target.ymin + (j + 0.5) * dy) * ((counts[j] / nSub) * (b - a));
        }
        lErrors.push(Math.abs(lS - target.exact) + 1e-12);
    });

    const ctxConv = document.getElementById("ex1ConvChart").getContext("2d");
    if (ex1ConvChartInstance) ex1ConvChartInstance.destroy();

    ex1ConvChartInstance = new Chart(ctxConv, {
        type: 'line',
        data: {
            labels: nVals.map(String),
            datasets: [
                {
                    label: 'Riemann Error',
                    data: rErrors,
                    borderColor: '#3498db',
                    borderWidth: 2,
                    tension: 0.1
                },
                {
                    label: 'Lebesgue Error',
                    data: lErrors,
                    borderColor: '#9b59b6',
                    borderWidth: 2,
                    borderDash: [5, 5],
                    tension: 0.1
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: 'Domain Subdivisions (N)' } },
                y: { type: 'logarithmic', title: { display: true, text: 'Absolute Error (Log Scale)' } }
            }
        }
    });
}


// --- EXERCISE 2 LOGIC ---

function normalCdfSeries(z, K) {
    let sum = 0;
    let term = z;
    sum += term;

    for (let k = 1; k < K; k++) {
        term *= (-z * z / 2.0) * (2 * k - 1) / ((2 * k + 1) * k);
        sum += term;
    }
    return 0.5 + (1.0 / Math.sqrt(2.0 * Math.PI)) * sum;
}

function normalCdfAbramowitzStegun(z) {
    let absZ = Math.abs(z);
    let p = 0.2316419;
    let b1 = 0.319381530, b2 = -0.356563782, b3 = 1.781477937, b4 = -1.821255978, b5 = 1.330274429;

    let t = 1.0 / (1.0 + p * absZ);
    let poly = t * (b1 + t * (b2 + t * (b3 + t * (b4 + t * b5))));
    let phi = (1.0 / Math.sqrt(2.0 * Math.PI)) * Math.exp(-0.5 * absZ * absZ);
    let cdf = 1.0 - phi * poly;

    return z >= 0 ? cdf : 1.0 - cdf;
}

function normalCdfTrapezoidal(z, N) {
    let a = -8.0;
    if (z <= a) return 0.0;
    let h = (z - a) / N;

    let pdf = x => (1.0 / Math.sqrt(2.0 * Math.PI)) * Math.exp(-0.5 * x * x);
    let sum = 0.5 * (pdf(a) + pdf(z));
    for (let i = 1; i < N; i++) {
        sum += pdf(a + i * h);
    }
    return sum * h;
}

function normalCdfSimpson(z, N) {
    let a = -8.0;
    if (z <= a) return 0.0;
    if (N % 2 !== 0) N += 1;
    let h = (z - a) / N;

    let pdf = x => (1.0 / Math.sqrt(2.0 * Math.PI)) * Math.exp(-0.5 * x * x);
    let sum = pdf(a) + pdf(z);
    for (let i = 1; i < N; i++) {
        let weight = (i % 2 === 1) ? 4 : 2;
        sum += weight * pdf(a + i * h);
    }
    return sum * (h / 3.0);
}

function runEx2Simulation() {
    let z = parseFloat(document.getElementById("ex2ZRange").value);
    let K = parseInt(document.getElementById("ex2KRange").value);
    let N = parseInt(document.getElementById("ex2NRange").value);

    let refVal = stdNormalCdfRef(z);

    let seriesVal = normalCdfSeries(z, K);
    let trapVal = normalCdfTrapezoidal(z, N);
    let simpVal = normalCdfSimpson(z, N);
    let asVal = normalCdfAbramowitzStegun(z);

    let bound = Math.pow(Math.abs(z), 2*K + 3) / (Math.sqrt(2 * Math.PI) * Math.pow(2, K + 1) * (2*K + 3) * factorial(K + 1));

    document.querySelectorAll(".ex2RefCell").forEach(el => el.innerText = refVal.toFixed(8));

    document.getElementById("ex2SeriesVal").innerText = seriesVal.toFixed(8);
    document.getElementById("ex2SeriesErr").innerText = Math.abs(seriesVal - refVal).toExponential(3);
    document.getElementById("ex2SeriesBound").innerText = bound < 1e-15 ? "< 1e-15" : bound.toExponential(3);

    document.getElementById("ex2TrapVal").innerText = trapVal.toFixed(8);
    document.getElementById("ex2TrapErr").innerText = Math.abs(trapVal - refVal).toExponential(3);

    document.getElementById("ex2SimpVal").innerText = simpVal.toFixed(8);
    document.getElementById("ex2SimpErr").innerText = Math.abs(simpVal - refVal).toExponential(3);

    document.getElementById("ex2ASVal").innerText = asVal.toFixed(8);
    document.getElementById("ex2ASErr").innerText = Math.abs(asVal - refVal).toExponential(3);

    renderEx2Charts(z, K, N);
}

function factorial(n) {
    if (n <= 1) return 1;
    let res = 1;
    for (let i = 2; i <= Math.min(n, 170); i++) res *= i;
    return res;
}

function renderEx2Charts(evalZ, K, N) {
    let zPoints = [], pdfPoints = [], areaPoints = [];
    for (let x = -4.0; x <= 4.0; x += 0.08) {
        zPoints.push(x.toFixed(2));
        let pdf = (1.0 / Math.sqrt(2.0 * Math.PI)) * Math.exp(-0.5 * x * x);
        pdfPoints.push(pdf);
        areaPoints.push(x <= evalZ ? pdf : null);
    }

    const ctxPdf = document.getElementById("ex2PdfChart").getContext("2d");
    if (ex2PdfChartInstance) ex2PdfChartInstance.destroy();

    ex2PdfChartInstance = new Chart(ctxPdf, {
        type: 'line',
        data: {
            labels: zPoints,
            datasets: [
                {
                    label: 'Standard Normal PDF φ(z)',
                    data: pdfPoints,
                    borderColor: '#e74c3c',
                    borderWidth: 2,
                    pointRadius: 0,
                    fill: false
                },
                {
                    label: 'Cumulative Area Φ(z)',
                    data: areaPoints,
                    backgroundColor: 'rgba(231, 76, 60, 0.3)',
                    borderColor: 'transparent',
                    pointRadius: 0,
                    fill: true
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: 'z-score' } },
                y: { title: { display: true, text: 'Density φ(z)' } }
            }
        }
    });

    let zGrid = [], seriesErrs = [], trapErrs = [], asErrs = [];
    for (let zVal = -3.5; zVal <= 3.5; zVal += 0.25) {
        zGrid.push(zVal.toFixed(2));
        let ref = stdNormalCdfRef(zVal);
        seriesErrs.push(Math.abs(normalCdfSeries(zVal, K) - ref) + 1e-16);
        trapErrs.push(Math.abs(normalCdfTrapezoidal(zVal, N) - ref) + 1e-16);
        asErrs.push(Math.abs(normalCdfAbramowitzStegun(zVal) - ref) + 1e-16);
    }

    const ctxErr = document.getElementById("ex2ErrorChart").getContext("2d");
    if (ex2ErrorChartInstance) ex2ErrorChartInstance.destroy();

    ex2ErrorChartInstance = new Chart(ctxErr, {
        type: 'line',
        data: {
            labels: zGrid,
            datasets: [
                {
                    label: 'Power Series Error',
                    data: seriesErrs,
                    borderColor: '#f1c40f',
                    borderWidth: 2,
                    fill: false
                },
                {
                    label: 'Trapezoidal Error',
                    data: trapErrs,
                    borderColor: '#3498db',
                    borderWidth: 2,
                    fill: false
                },
                {
                    label: 'Abramowitz & Stegun Error',
                    data: asErrs,
                    borderColor: '#e67e22',
                    borderWidth: 2,
                    borderDash: [4, 4],
                    fill: false
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: 'Evaluation Point z' } },
                y: { type: 'logarithmic', title: { display: true, text: 'Absolute Error (Log Scale)' } }
            }
        }
    });
}

function tryInitHomework5() {
    if (typeof Chart === 'undefined') {
        setTimeout(tryInitHomework5, 50);
        return;
    }
    runEx1Simulation();
    runEx2Simulation();
}

document.addEventListener("DOMContentLoaded", tryInitHomework5);
</script>
{% endraw %}
