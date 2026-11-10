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
2. **Numerical Computation of the Standard Normal CDF ($\Phi(z)$):** Examining the mathematical barrier imposed by **Liouville's theorem** on non-elementary antiderivatives, deriving series expansions from probability density functions, and evaluating quadrature versus rational approximations for statistical computing.

---

### 1. Exercise 1: Riemann and Lebesgue Integration

#### Theoretical Foundations

The classical **Riemann integral** partitions the domain interval $[a, b]$ into $N$ subintervals $\Delta x_i = x_i - x_{i-1}$. Using sample points $x_i^* \in [x_{i-1}, x_i]$, the Riemann sum is defined as:

$$
R_N = \sum_{i=1}^N f(x_i^*) \Delta x_i
$$

A function $f$ is Riemann-integrable on $[a, b]$ if and only if $f$ is bounded and its set of discontinuities has Lebesgue measure zero (**Lebesgue's criterion for Riemann integrability**). Equivalently, the upper and lower Darboux sums must converge to the same value.

Conversely, the **Lebesgue integral** partitions the range $[y_{\min}, y_{\max}]$ into $M$ subintervals $[y_j, y_{j+1})$. For each level-set interval, we evaluate the domain measure of its preimage $E_j = \{x \in [a, b] : y_j \le f(x) < y_{j+1}\}$. The Lebesgue simple-function approximation sum is given by:

$$
L_M = \sum_{j=0}^{M-1} y_j^* \, \mu(E_j)
$$

where $y_j^* \in [y_j, y_{j+1})$ (in the implementation below, the lower bound $y_j$) and $\mu(E_j)$ denotes the 1D Lebesgue measure (length) of the subset $E_j \subset [a, b]$. For a bounded measurable $f$, the error of this sum is at most $(y_{\max}-y_{\min})(b-a)/M$.

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
| **Discontinuities** | Fails if discontinuities have positive measure | Theoretically unaffected (needs only measurability); a *sampled* estimate of $\mu(E_j)$ does not inherit this immunity |
| **Unbounded Functions** | Requires improper limit processes | Integrates unbounded functions if $\int \lvert f\rvert \, d\mu < \infty$ |
| **Computational Complexity** | $O(N)$ grid evaluations | $O(N)$ evaluations plus $O(M)$ bins when $\mu(E_j)$ is estimated by histogram binning; closed-form $\mu(E_j)$ must be known analytically for pathological sets |
| **Pathological Sets** | Fails on $\mathbf{1}_{\mathbb{Q}}(x)$ (Darboux upper sum $= 1$, lower sum $= 0$) | Correctly evaluates $\mathbf{1}_{\mathbb{Q}}(x) = 0$ since $\mu(\mathbb{Q} \cap [0,1]) = 0$ |

#### Evaluated Functions Suite

1. **$f_1(x) = \sin(x)$ on $[0, \pi]$:** Smooth, continuous. Both methods converge to the exact value $2.0$.
2. **$f_2(x) = x^2$ on $[0, 1]$:** Monotonic, continuous. Converges to $1/3 \approx 0.333333$.
3. **$$f_3(x) = 3 \cdot \mathbf{1}_{\{x \ge 0.5\}} + \mathbf{1}_{\{x < 0.5\}}$$ on $[0, 1]$:** Step discontinuity at $x=0.5$. Exact integral $= 2.0$.
4. **$f_4(x) = \lvert x - 0.5\rvert$ on $[0, 1]$:** Non-differentiable peak at $x=0.5$. Exact integral $= 0.25$.
5. **$f_5(x) = 1 / \sqrt{x}$ on $(0, 1]$:** Unbounded singularity at $x=0$. Improper Riemann (and Lebesgue) integral $= 2.0$. *Numerically*, the interval is truncated to $[\varepsilon, 1]$ with $\varepsilon = 10^{-4}$, whose exact integral is $2 - 2\sqrt{\varepsilon} = 1.98$; this is the reference used in the simulation, and $f_5 \le 100$ there.
6. **$f_6(x) = \sin(x)/x$ on $(0, \pi]$:** Non-trivial function (Sine integral $\text{Si}(\pi) \approx 1.851937$). Numerically truncated to $[10^{-4}, \pi]$, so the reference is $\text{Si}(\pi) - \text{Si}(10^{-4}) \approx 1.851837$.
7. **$f_7(x) = \mathbf{1}_{\mathbb{Q}}(x)$ on $[0, 1]$ (Dirichlet Function):** Everywhere discontinuous. The Darboux upper sum is $1.0$ and the lower sum is $0.0$ for every partition, so $f_7$ is not Riemann integrable (a Riemann sum equals $1$ with rational tags and $0$ with irrational tags). The Lebesgue integral is $0.0$ because $\mu(\mathbb{Q}\cap[0,1]) = 0$. Since a computer can only sample rational points, $\mu(E_j)$ is supplied analytically in the simulation.
8. **$f_8(x) = 1/q$ if $x=p/q \in \mathbb{Q}$ (lowest terms) else $0$ (Thomae's / Popcorn Function):** Discontinuous at rationals, continuous at irrationals. Both Riemann and Lebesgue integrals evaluate to $0.0$ (each level set $\{f = 1/q\}$ is finite, hence has measure zero).

---

### 2. Exercise 2: Numerical Computation of the Normal CDF ($\Phi(z)$)

#### Non-Elementary Integrals and Liouville's Theorem

The Probability Density Function (PDF) of a Standard Normal random variable $Z \sim \mathcal{N}(0,1)$ is given by:

$$
\phi(z) = \frac{1}{\sqrt{2\pi}} e^{-z^2/2}
$$

The Cumulative Distribution Function (CDF) $\Phi(z) = P(Z \le z)$ requires computing:

$$
\Phi(z) = \int_{-\infty}^z \frac{1}{\sqrt{2\pi}} e^{-t^2/2} \, dt = \frac{1}{2} + \frac{1}{\sqrt{2\pi}} \int_0^z e^{-t^2/2} \, dt
$$

In introductory statistics, $\Phi(z)$ is always provided via pre-computed lookup tables or software routines. This is not merely a convenience, but a consequence of **Liouville's Theorem on Integration in Finite Terms** (1835).

> **Liouville's Theorem:** Let $f(x)$ and $g(x)$ be rational functions with $g(x)$ non-constant. The indefinite integral $\int f(x) e^{g(x)} dx$ can be expressed in terms of elementary functions (polynomials, exponentials, logarithms, trigonometric functions, and their compositions) **if and only if** there exists a rational function $r(x)$ satisfying the differential equation:
>
> $$
> f(x) = r'(x) + r(x)g'(x)
> $$

For the standard normal integrand, $f(x) = 1$ and $g(x) = -x^2/2$, so $g'(x) = -x$. The Liouville condition becomes:

$$
1 = r'(x) - x \cdot r(x)
$$

If $r(x) = \frac{P(x)}{Q(x)}$ is a rational function in lowest terms, analyzing degrees (and poles) shows that no rational function can satisfy this identity. Consequently, **$\int e^{-x^2/2} dx$ has no elementary antiderivative**.

*Remark.* The obstruction concerns the **antiderivative** only. The definite integral over the whole line is elementary: $\int_{-\infty}^{\infty} e^{-t^2/2}\,dt = \sqrt{2\pi}$ (via the polar-coordinates trick). What cannot be written in finite terms is $\Phi(z)$ for a generic finite $z$.

#### Analytical Representation via Special Functions & Power Series

To bypass this barrier, mathematics defines the **Error Function** $\operatorname{erf}(x)$ as a standardized special function:

$$
\operatorname{erf}(x) = \frac{2}{\sqrt{\pi}} \int_0^x e^{-t^2} dt
$$

By substituting $t = u / \sqrt{2}$, the standard normal CDF $\Phi(z)$ is exactly related to $\operatorname{erf}(z)$ by:

$$
\Phi(z) = \frac{1}{2} \left[ 1 + \operatorname{erf}\left(\frac{z}{\sqrt{2}}\right) \right]
$$

As highlighted in the *Hogg-McKean-Craig Section 3.4 Notes*, an explicit power series representation can be derived by expanding the Maclaurin series of $e^{-t^2/2}$ and integrating term-by-term (justified by uniform convergence on compact sets):

$$
e^{-t^2/2} = \sum_{k=0}^{\infty} \frac{(-1)^k t^{2k}}{2^k k!} \implies \int_0^z e^{-t^2/2} dt = \sum_{k=0}^{\infty} \frac{(-1)^k z^{2k+1}}{2^k (2k+1) k!}
$$

Thus, the exact analytical series for $\Phi(z)$ is:

$$
\Phi(z) = \frac{1}{2} + \frac{1}{\sqrt{2\pi}} \sum_{k=0}^{\infty} \frac{(-1)^k z^{2k+1}}{2^k (2k+1) k!}
$$

The series is alternating, and the ratio of consecutive terms is

$$
\left|\frac{T_{k+1}}{T_k}\right| = \frac{z^2 (2k+1)}{2(2k+3)(k+1)},
$$

which is smaller than $1$ only for $k \gtrsim z^2/2$. Hence the terms are **not** monotonically decreasing from the start: for $z = 4$ they grow until $k \approx 8$ (peak magnitude $\approx 10^2$) and only then decay, which causes cancellation and a loss of about two digits in double precision. Once the terms are decreasing ($K \gtrsim z^2/2$), the **Alternating Series Estimation Theorem** bounds the truncation error of the sum of the first $K$ terms ($k = 0,\dots,K-1$) by the first omitted term:

$$
\left| R_K(z) \right| \le \frac{|z|^{2K+1}}{\sqrt{2\pi} \, 2^{K} (2K+1) \, K!}
$$

#### Numerical Methods Implemented

1. **Power Series Expansion:** Truncated sum of $K$ terms. Accurate to near machine precision for $\lvert z\rvert \le 3$ with roughly 30 terms; up to $\lvert z\rvert = 4$ it still gives about $10^{-13}$ absolute accuracy but needs more terms and suffers cancellation (see above). It is also used (with 200 terms) as the reference value in the simulation.
2. **Numerical Quadrature (Trapezoidal & Simpson's Rule):** Numerically integrates $\phi(t)$ from a finite lower bound $-8.0$ (where $\Phi(-8)\approx 6\times10^{-16}$) up to $z$ across $N$ panels. The composite trapezoidal rule is $\mathcal{O}(h^2)$; its leading error comes from the finite endpoint $z$ (about $h^2 \phi'(z)/12$), because on the whole real line the trapezoidal rule applied to a Gaussian converges exponentially fast. Simpson's rule is $\mathcal{O}(h^4)$.
3. **Abramowitz & Stegun Rational Approximation (formula 26.2.17):** A polynomial-rational formula ($O(1)$ complexity) widely used in production libraries:
   $$
   t = \frac{1}{1 + p\lvert z\rvert}, \quad \Phi(z) \approx 1 - \phi(z) \left( b_1 t + b_2 t^2 + b_3 t^3 + b_4 t^4 + b_5 t^5 \right) \quad \text{for } z \ge 0
   $$
   with $p = 0.2316419$, $b_1 = 0.319381530$, $b_2 = -0.356563782$, $b_3 = 1.781477937$, $b_4 = -1.821255978$, $b_5 = 1.330274429$, and absolute error $\lvert\epsilon(z)\rvert < 7.5 \times 10^{-8}$. For $z < 0$ use $\Phi(z) = 1 - \Phi(-z)$, computed directly as $\phi(z)(\cdots)$ to avoid cancellation. (Do not confuse it with A&S 7.1.26 for $\operatorname{erf}$, which has different coefficients and a maximum error of about $1.5\times10^{-7}$.)

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
                    <option value="step">f3(x) = Step Function (1 if x&lt;0.5 else 3) on [0, 1]</option>
                    <option value="abs">f4(x) = |x - 0.5| on [0, 1]</option>
                    <option value="inv_sqrt">f5(x) = 1 / sqrt(x) on [1e-4, 1] (Unbounded, truncated)</option>
                    <option value="sinc">f6(x) = sin(x) / x on [1e-4, π]</option>
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
                        <td class="font-weight-bold text-left">Lebesgue Preimage Sum ($L_M$, lower endpoints)</td>
                        <td id="ex1LVal" style="color: #9b59b6; font-weight: bold;">-</td>
                        <td class="ex1ExactCell">-</td>
                        <td id="ex1LErr">-</td>
                        <td id="ex1LTime">-</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <p id="ex1Note" class="small text-muted mb-0"></p>
        <!-- Ex 1 Charts -->
        <div class="row mt-4">
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Function Shape</h6>
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
                        <th>Reference $\Phi(z)$ (200-term series)</th>
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
                        <td class="font-weight-bold text-left">Abramowitz &amp; Stegun 26.2.17</td>
                        <td id="ex2ASVal" style="color: #e67e22; font-weight: bold;">-</td>
                        <td class="ex2RefCell">-</td>
                        <td id="ex2ASErr">-</td>
                        <td>$\lt 7.5 \times 10^{-8}$</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <p class="small text-muted mb-0">The series bound is shown only when the terms are already decreasing ($K \gtrsim z^2/2$), which is the condition for the Alternating Series Estimation Theorem to apply.</p>
        <!-- Ex 2 Charts -->
        <div class="row mt-4">
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Standard Normal PDF &amp; Area under $\Phi(z)$</h6>
                <div style="position: relative; width: 100%; height: 250px;">
                    <canvas id="ex2PdfChart"></canvas>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center">Absolute Error across $z \in [-3.5, 3.5]$</h6>
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
   * For continuous and piecewise continuous functions ($f_1$ through $f_4$), both Riemann and Lebesgue numerical integration converge. Lebesgue's sum with lower endpoints converges at rate $O(1/M)$ in the number of range bins.
   * For Dirichlet's function ($f_7$), the Darboux upper and lower sums are $1$ and $0$ for every partition, so the Riemann integral does not exist (a Riemann sum equals $1$ or $0$ depending on whether the sample points are rational or irrational). Lebesgue integration isolates the preimage of $1.0$ as $\mathbb{Q} \cap [0,1]$, which has Lebesgue measure $0$, yielding the integral $0.0$.
2. **Computational Footprint of Range Partitioning:**
   * Numerical Lebesgue integration requires mapping range level-sets back to domain preimage measures $\mu(E_j)$. When $\mu(E_j)$ is *estimated* by counting equispaced samples (as in this simulation) the method is in practice a regrouped Riemann sum and does **not** inherit the theoretical immunity to jumps or dense discontinuities: that advantage holds only when the measures $\mu(E_j)$ are known exactly, as for $f_7$ and $f_8$.
3. **The Necessity of Numerical CDF Routines:**
   * Liouville's theorem shows that the lack of an elementary antiderivative for $e^{-z^2/2}$ is an intrinsic algebraic property of the differential field of elementary functions, not a limitation of calculus (while $\int_{\mathbb{R}} e^{-t^2/2}dt = \sqrt{2\pi}$ is elementary).
   * The **power series** (Hogg-McKean-Craig Sec. 3.4) gives arbitrary accuracy for small to moderate $\lvert z\rvert$ ($\lvert z\rvert \le 3$ in double precision with about 30 terms), but suffers cancellation for larger $\lvert z\rvert$.
   * The **Abramowitz & Stegun** approximation costs $O(1)$ and has a uniform *absolute* error below $7.5\times10^{-8}$. In the tails this is not enough in relative terms: e.g. $\Phi(-4) \approx 3.17\times10^{-5}$ gives a relative error up to about $0.2\%$. For tail probabilities, use $\operatorname{erfc}$-based algorithms or continued fractions.

<!-- Scripts for interactive tools -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
{% raw %}
<script>
let ex1FuncChartInstance = null;
let ex1ConvChartInstance = null;
let ex2PdfChartInstance = null;
let ex2ErrorChartInstance = null;

// ---------- Shared helpers ----------

// Destroys any chart already attached to the canvas (even one whose instance
// variable was lost) before creating the new one: avoids "Canvas is already in use".
function makeChart(canvasId, config) {
    const canvas = document.getElementById(canvasId);
    const old = Chart.getChart(canvas);
    if (old) old.destroy();
    return new Chart(canvas.getContext("2d"), config);
}

function factorial(n) {
    if (n <= 1) return 1;
    let res = 1;
    for (let i = 2; i <= Math.min(n, 170); i++) res *= i;
    return res;
}

const SQRT_2PI = Math.sqrt(2.0 * Math.PI);
const stdPdf = x => Math.exp(-0.5 * x * x) / SQRT_2PI;

// ---------- EXERCISE 1 LOGIC ----------

const EX1_NOTES = {
    default: "The Lebesgue sum estimates each μ(E_j) by counting samples of f on a fine grid and uses the lower endpoint y_j of each bin. Its error is at most (ymax − ymin)(b − a)/M plus sampling error, so it decreases with M, not with N. Because the measures are estimated from a grid, this is a regrouped Riemann sum.",
    inv_sqrt: "Numerically the interval is [1e-4, 1] (f ≤ 100), so the reference value is 2 − 2·√(1e-4) = 1.98, not 2. Midpoint/trapezoid converge slowly near the singularity; the Lebesgue sum needs many bins because the range [1, 100] is wide.",
    sinc: "Numerically the interval is [1e-4, π]; the reference is Si(π) − Si(1e-4) ≈ 1.851837.",
    dirichlet: "Every floating-point number is rational, so any grid sample gives f = 1 and the Riemann sums equal 1 (with irrational tags they would equal 0: the Riemann sums have no limit, and the Darboux sums are 1 and 0). A grid cannot 'see' a null set, so the Lebesgue sum uses the analytic measures μ({f=1}) = 0 and μ({f=0}) = 1, giving L = 0·1 + 1·0 = 0.",
    thomae: "The Riemann sums are small positive numbers (f(0) = f(1) = 1 by convention adds a term of order 1/N to the trapezoid). The Lebesgue sum uses analytic measures: μ({f = 0}) = 1 and μ({f = 1/q}) = 0 for every q (finite sets), so L = 0."
};

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
        case 'inv_sqrt': {
            const a = 1e-4;
            return { f: x => 1.0 / Math.sqrt(x), a: a, b: 1.0, exact: 2 - 2 * Math.sqrt(a), ymin: 1.0, ymax: 100.0, note: 'inv_sqrt' };
        }
        case 'sinc':
            // Si(pi) - Si(1e-4), with Si(pi) = 1.851937051982...
            return { f: x => x === 0 ? 1.0 : Math.sin(x) / x, a: 1e-4, b: Math.PI, exact: 1.851937051982 - 1e-4, ymin: 0, ymax: 1.0, note: 'sinc' };
        case 'dirichlet':
            return {
                f: x => 1.0, // every floating-point number is rational
                a: 0, b: 1.0, exact: 0.0, ymin: 0, ymax: 1.0,
                levelSets: () => [{ y: 0, mu: 1 }, { y: 1, mu: 0 }],
                note: 'dirichlet'
            };
        case 'thomae':
            return {
                f: x => {
                    const eps = 1e-6;
                    for (let q = 1; q <= 100; q++) {
                        const p = Math.round(x * q);
                        if (Math.abs(x - p / q) < eps) return 1.0 / q;
                    }
                    return 0.0;
                },
                a: 0, b: 1.0, exact: 0.0, ymin: 0, ymax: 1.0,
                levelSets: () => {
                    const s = [{ y: 0, mu: 1 }];
                    for (let q = 1; q <= 100; q++) s.push({ y: 1 / q, mu: 0 });
                    return s;
                },
                note: 'thomae'
            };
        default:
            return getEx1Func('sin');
    }
}

function riemannMidpoint(f, a, b, N) {
    const dx = (b - a) / N;
    let s = 0;
    for (let i = 0; i < N; i++) s += f(a + (i + 0.5) * dx);
    return s * dx;
}

function riemannTrapezoid(f, a, b, N) {
    const dx = (b - a) / N;
    let s = 0.5 * (f(a) + f(b));
    for (let i = 1; i < N; i++) s += f(a + i * dx);
    return s * dx;
}

// Lebesgue sum: sum_j y_j * mu(E_j), with y_j the LOWER endpoint of bin j.
// mu(E_j) is estimated by counting Nsub midpoint samples, unless the level-set
// measures are known analytically (Dirichlet, Thomae).
function lebesgueSum(target, Nsub, M) {
    if (target.levelSets) {
        return target.levelSets().reduce((s, e) => s + e.y * e.mu, 0);
    }
    const f = target.f, a = target.a, b = target.b;
    const ymin = target.ymin, ymax = target.ymax;
    const dy = (ymax - ymin) / M;
    const counts = new Array(M).fill(0);
    const h = (b - a) / Nsub;
    for (let k = 0; k < Nsub; k++) {
        const y = f(a + (k + 0.5) * h);
        let idx = Math.floor((y - ymin) / dy);
        if (idx >= M) idx = M - 1;
        if (idx >= 0) counts[idx]++;
    }
    let s = 0;
    for (let j = 0; j < M; j++) {
        s += (ymin + j * dy) * (counts[j] / Nsub) * (b - a);
    }
    return s;
}

function runEx1Simulation() {
    const funcType = document.getElementById("ex1FuncSelect").value;
    const N = parseInt(document.getElementById("ex1NRange").value);
    const M = parseInt(document.getElementById("ex1MRange").value);

    const target = getEx1Func(funcType);
    const f = target.f, a = target.a, b = target.b, exact = target.exact;

    let t0 = performance.now();
    const rSum = riemannMidpoint(f, a, b, N);
    const rTime = (performance.now() - t0).toFixed(2);

    t0 = performance.now();
    const trapSum = riemannTrapezoid(f, a, b, N);
    const trapTime = (performance.now() - t0).toFixed(2);

    t0 = performance.now();
    const lSum = lebesgueSum(target, Math.max(N * 4, 2000), M);
    const lTime = (performance.now() - t0).toFixed(2);

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

    document.getElementById("ex1Note").innerText = EX1_NOTES[target.note] || EX1_NOTES.default;

    try {
        renderEx1Charts(target, M);
    } catch (e) {
        console.error("Ex1 chart error:", e);
    }
}

function renderEx1Charts(target, M) {
    const f = target.f, a = target.a, b = target.b;

    const plotPoints = 120;
    const xLabels = [], yData = [];
    const step = (b - a) / plotPoints;
    for (let i = 0; i <= plotPoints; i++) {
        const x = a + i * step;
        xLabels.push(x.toFixed(2));
        yData.push(f(x));
    }

    ex1FuncChartInstance = makeChart("ex1FuncChart", {
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
            animation: false,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: 'Domain (x)' } },
                y: { title: { display: true, text: 'Range f(x)' } }
            }
        }
    });

    const nVals = [10, 20, 50, 100, 200, 500, 1000];
    const rErrors = [], lErrors = [];
    nVals.forEach(n => {
        rErrors.push(Math.abs(riemannMidpoint(f, a, b, n) - target.exact) + 1e-12);
        lErrors.push(Math.abs(lebesgueSum(target, Math.max(4 * n, 2000), M) - target.exact) + 1e-12);
    });

    ex1ConvChartInstance = makeChart("ex1ConvChart", {
        type: 'line',
        data: {
            labels: nVals.map(String),
            datasets: [
                {
                    label: 'Riemann (midpoint) Error',
                    data: rErrors,
                    borderColor: '#3498db',
                    borderWidth: 2,
                    tension: 0.1
                },
                {
                    label: 'Lebesgue Error (M fixed)',
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
            animation: false,
            scales: {
                x: { title: { display: true, text: 'Domain Subdivisions (N)' } },
                y: { type: 'logarithmic', title: { display: true, text: 'Absolute Error (Log Scale)' } }
            }
        }
    });
}

// ---------- EXERCISE 2 LOGIC ----------

// Sum of the first K terms (k = 0..K-1) of the Maclaurin-based series.
function normalCdfSeries(z, K) {
    let term = z;
    let sum = term;
    for (let k = 1; k < K; k++) {
        term *= (-z * z / 2.0) * (2 * k - 1) / ((2 * k + 1) * k);
        sum += term;
    }
    return 0.5 + sum / SQRT_2PI;
}

// Reference: 200-term series (accurate to ~1e-13 for |z| <= 4)
function stdNormalCdfRef(z) {
    return normalCdfSeries(z, 200);
}

// Abramowitz & Stegun 26.2.17 (|error| < 7.5e-8)
function normalCdfAbramowitzStegun(z) {
    const absZ = Math.abs(z);
    const p = 0.2316419;
    const b1 = 0.319381530, b2 = -0.356563782, b3 = 1.781477937, b4 = -1.821255978, b5 = 1.330274429;

    const t = 1.0 / (1.0 + p * absZ);
    const poly = t * (b1 + t * (b2 + t * (b3 + t * (b4 + t * b5))));
    const tail = stdPdf(absZ) * poly; // = 1 - Phi(|z|), computed without cancellation
    return z >= 0 ? 1.0 - tail : tail;
}

function normalCdfTrapezoidal(z, N) {
    const a = -8.0;
    if (z <= a) return 0.0;
    const h = (z - a) / N;
    let sum = 0.5 * (stdPdf(a) + stdPdf(z));
    for (let i = 1; i < N; i++) sum += stdPdf(a + i * h);
    return sum * h;
}

function normalCdfSimpson(z, N) {
    const a = -8.0;
    if (z <= a) return 0.0;
    if (N % 2 !== 0) N += 1;
    const h = (z - a) / N;
    let sum = stdPdf(a) + stdPdf(z);
    for (let i = 1; i < N; i++) {
        sum += ((i % 2 === 1) ? 4 : 2) * stdPdf(a + i * h);
    }
    return sum * (h / 3.0);
}

// Bound on the truncation error after K terms (k = 0..K-1): first omitted term k = K.
function seriesErrorBound(z, K) {
    return Math.pow(Math.abs(z), 2 * K + 1) /
        (SQRT_2PI * Math.pow(2, K) * (2 * K + 1) * factorial(K));
}

function runEx2Simulation() {
    const z = parseFloat(document.getElementById("ex2ZRange").value);
    const K = parseInt(document.getElementById("ex2KRange").value);
    const N = parseInt(document.getElementById("ex2NRange").value);

    const refVal = stdNormalCdfRef(z);

    const seriesVal = normalCdfSeries(z, K);
    const trapVal = normalCdfTrapezoidal(z, N);
    const simpVal = normalCdfSimpson(z, N);
    const asVal = normalCdfAbramowitzStegun(z);

    document.querySelectorAll(".ex2RefCell").forEach(el => el.innerText = refVal.toFixed(8));

    document.getElementById("ex2SeriesVal").innerText = seriesVal.toFixed(8);
    document.getElementById("ex2SeriesErr").innerText = Math.abs(seriesVal - refVal).toExponential(3);

    let boundText;
    if (K < z * z / 2) {
        boundText = "n/a (terms still growing: K < z²/2)";
    } else {
        const bound = seriesErrorBound(z, K);
        boundText = bound < 1e-15 ? "< 1e-15" : bound.toExponential(3);
    }
    document.getElementById("ex2SeriesBound").innerText = boundText;

    document.getElementById("ex2TrapVal").innerText = trapVal.toFixed(8);
    document.getElementById("ex2TrapErr").innerText = Math.abs(trapVal - refVal).toExponential(3);

    document.getElementById("ex2SimpVal").innerText = simpVal.toFixed(8);
    document.getElementById("ex2SimpErr").innerText = Math.abs(simpVal - refVal).toExponential(3);

    document.getElementById("ex2ASVal").innerText = asVal.toFixed(8);
    document.getElementById("ex2ASErr").innerText = Math.abs(asVal - refVal).toExponential(3);

    try {
        renderEx2Charts(z, K, N);
    } catch (e) {
        console.error("Ex2 chart error:", e);
    }
}

function renderEx2Charts(evalZ, K, N) {
    const zPoints = [], pdfPoints = [], areaPoints = [];
    for (let i = 0; i <= 100; i++) {
        const x = -4.0 + i * 0.08;
        zPoints.push(x.toFixed(2));
        const pdf = stdPdf(x);
        pdfPoints.push(pdf);
        areaPoints.push(x <= evalZ ? pdf : null);
    }

    ex2PdfChartInstance = makeChart("ex2PdfChart", {
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
            animation: false,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: 'z-score' } },
                y: { title: { display: true, text: 'Density φ(z)' } }
            }
        }
    });

    const zGrid = [], seriesErrs = [], trapErrs = [], simpErrs = [], asErrs = [];
    for (let i = 0; i <= 28; i++) {
        const zVal = -3.5 + i * 0.25;
        zGrid.push(zVal.toFixed(2));
        const ref = stdNormalCdfRef(zVal);
        seriesErrs.push(Math.abs(normalCdfSeries(zVal, K) - ref) + 1e-16);
        trapErrs.push(Math.abs(normalCdfTrapezoidal(zVal, N) - ref) + 1e-16);
        simpErrs.push(Math.abs(normalCdfSimpson(zVal, N) - ref) + 1e-16);
        asErrs.push(Math.abs(normalCdfAbramowitzStegun(zVal) - ref) + 1e-16);
    }

    ex2ErrorChartInstance = makeChart("ex2ErrorChart", {
        type: 'line',
        data: {
            labels: zGrid,
            datasets: [
                { label: 'Power Series Error', data: seriesErrs, borderColor: '#f1c40f', borderWidth: 2, fill: false },
                { label: 'Trapezoidal Error', data: trapErrs, borderColor: '#3498db', borderWidth: 2, fill: false },
                { label: "Simpson's Error", data: simpErrs, borderColor: '#2ecc71', borderWidth: 2, fill: false },
                { label: 'Abramowitz & Stegun Error', data: asErrs, borderColor: '#e67e22', borderWidth: 2, borderDash: [4, 4], fill: false }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            animation: false,
            scales: {
                x: { title: { display: true, text: 'Evaluation Point z' } },
                y: { type: 'logarithmic', title: { display: true, text: 'Absolute Error (Log Scale)' } }
            }
        }
    });
}

// ---------- Initialisation ----------

function tryInitHomework5() {
    if (typeof Chart === 'undefined') {
        setTimeout(tryInitHomework5, 50);
        return;
    }
    runEx1Simulation();
    runEx2Simulation();
    // Extra safeguard: also bind the select via addEventListener
    const sel = document.getElementById("ex1FuncSelect");
    if (sel) sel.addEventListener("change", runEx1Simulation);
}

if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", tryInitHomework5);
} else {
    tryInitHomework5();
}
</script>
{% endraw %}
