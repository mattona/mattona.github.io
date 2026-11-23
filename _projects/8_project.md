---
layout: page
title: Modes of Convergence & Bayesian Statistical Inference
description: Convergence of random variables (LLN, CLT and the √n scaling) and Bayesian updating with prior, likelihood and posterior.
img: assets/img/hmw7.jpg
importance: 7
category: Statistics
permalink: /projects/homework7/
giscus_comments: true
---

This assignment covers two topics. The first is how a sequence of random variables can converge to a limit, how those notions show up in the Law of Large Numbers (LLN) and the Central Limit Theorem (CLT), and why the factor $$\sqrt{n}$$ keeps appearing. The second is Bayesian inference: how Bayes' theorem combines a **prior** with a **likelihood** to give a **posterior**, and how much the choice of prior matters. Both topics have an interactive simulation at the end of the page.

---

### 1. Modes of Convergence

#### The four definitions

Let $$(\Omega, \mathcal{F}, P)$$ be a probability space, let $$\{X_n\}_{n \ge 1}$$ be a sequence of real random variables and let $$X$$ be the limit. For the first three modes, $$X$$ is defined on the same space as the $$X_n$$. For convergence in distribution only the individual laws matter.

**Almost sure convergence** ($$X_n \xrightarrow{\text{a.s.}} X$$). For almost every outcome, the numbers $$X_n(\omega)$$ converge to $$X(\omega)$$:

$$
P\left(\left\{\omega \in \Omega : \lim_{n \to \infty} X_n(\omega) = X(\omega)\right\}\right) = 1
$$

An equivalent form is that, for every $$\varepsilon > 0$$,

$$
\lim_{m \to \infty} P\left(\sup_{n \ge m} \lvert X_n - X \rvert \ge \varepsilon\right) = 0
$$

**Mean-square convergence** ($$X_n \xrightarrow{L^2} X$$). Assuming $$\mathbb{E}[X_n^2]$$ and $$\mathbb{E}[X^2]$$ are finite, the expected squared error goes to zero:

$$
\lim_{n \to \infty} \mathbb{E}\left[(X_n - X)^2\right] = 0
$$

When the limit is a constant $$\theta$$, the error splits into variance plus squared bias:

$$
\mathbb{E}\left[(X_n - \theta)^2\right] = \operatorname{Var}(X_n) + \left(\mathbb{E}[X_n] - \theta\right)^2
$$

**Convergence in probability** ($$X_n \xrightarrow{P} X$$). For every tolerance $$\varepsilon > 0$$, the chance of missing the limit by more than $$\varepsilon$$ vanishes:

$$
\lim_{n \to \infty} P\left(\lvert X_n - X \rvert \ge \varepsilon\right) = 0
$$

This is weaker than almost sure convergence. A single sample path may still jump away from the limit infinitely often, as long as those jumps become rarer and rarer.

**Convergence in distribution** ($$X_n \xrightarrow{d} X$$). Write $$F_n$$ and $$F$$ for the CDFs of $$X_n$$ and $$X$$. Then

$$
\lim_{n \to \infty} F_n(x) = F(x) \qquad \text{at every point } x \text{ where } F \text{ is continuous}
$$

This only compares distributions. It says nothing about how $$X_n$$ and $$X$$ are related as functions on $$\Omega$$.

<div class="my-4 p-4 rounded z-depth-1" style="background-color: rgba(128,128,128,0.03); border: 1px solid rgba(150,150,150,0.25);" markdown="0">
    <div class="text-center mb-3">
        <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #3498db; font-size: 0.85rem;">
            HOW THE FOUR MODES RELATE
        </span>
        <p class="small text-muted mt-2 mb-0">Solid arrows always hold. Dashed arrows need an extra condition.</p>
    </div>

    <svg viewBox="0 0 760 215" class="w-100" style="max-height: 215px; display: block; overflow: visible;">
        <defs>
            <marker id="arrowSolid" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto">
                <path d="M 0 1 L 7 4 L 0 7 z" fill="#3498db" />
            </marker>
            <marker id="arrowDashed" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto">
                <path d="M 0 1 L 7 4 L 0 7 z" fill="#e67e22" />
            </marker>
        </defs>

        <rect x="20" y="40" width="190" height="62" rx="8" fill="#9b59b6" fill-opacity="0.15" stroke="#9b59b6" stroke-width="2"/>
        <text x="115" y="66" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">Almost sure (a.s.)</text>
        <text x="115" y="86" fill="#9b59b6" font-size="11" font-weight="bold" text-anchor="middle">P(lim Xₙ = X) = 1</text>

        <rect x="20" y="140" width="190" height="62" rx="8" fill="#2ecc71" fill-opacity="0.15" stroke="#2ecc71" stroke-width="2"/>
        <text x="115" y="166" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">Mean-square (L²)</text>
        <text x="115" y="186" fill="#2ecc71" font-size="11" font-weight="bold" text-anchor="middle">E[(Xₙ − X)²] → 0</text>

        <rect x="295" y="90" width="190" height="62" rx="8" fill="#3498db" fill-opacity="0.18" stroke="#3498db" stroke-width="2"/>
        <text x="390" y="116" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">In probability (P)</text>
        <text x="390" y="136" fill="#3498db" font-size="11" font-weight="bold" text-anchor="middle">P(|Xₙ − X| ≥ ε) → 0</text>

        <rect x="555" y="90" width="185" height="62" rx="8" fill="#e74c3c" fill-opacity="0.15" stroke="#e74c3c" stroke-width="2"/>
        <text x="647" y="116" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">In distribution (d)</text>
        <text x="647" y="136" fill="#e74c3c" font-size="11" font-weight="bold" text-anchor="middle">Fₙ(x) → F(x)</text>

        <line x1="210" y1="78" x2="292" y2="112" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="236" y="84" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">sup form</text>

        <line x1="210" y1="164" x2="292" y2="130" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="252" y="170" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">Chebyshev</text>

        <line x1="485" y1="108" x2="552" y2="108" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="518" y="98" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">always</text>

        <line x1="555" y1="134" x2="488" y2="134" stroke="#e67e22" stroke-width="1.8" stroke-dasharray="4,3" marker-end="url(#arrowDashed)"/>
        <text x="520" y="152" fill="#e67e22" font-size="10" font-weight="bold" text-anchor="middle">if X is a constant</text>

        <path d="M 330 90 Q 270 24, 213 52" fill="none" stroke="#e67e22" stroke-width="1.8" stroke-dasharray="4,3" marker-end="url(#arrowDashed)"/>
        <text x="270" y="16" fill="#e67e22" font-size="10" font-weight="bold" text-anchor="middle">if Σ P(|Xₙ − X| ≥ ε) &lt; ∞ (Borel–Cantelli)</text>
    </svg>
</div>

#### Where each mode shows up

| Mode | Main theorem | Statement | What it is used for |
| :--- | :--- | :--- | :--- |
| **Almost sure** | Strong Law of Large Numbers (Kolmogorov) | $$\bar{X}_n \xrightarrow{\text{a.s.}} \mu$$ for i.i.d. data with $$\mathbb{E}\lvert X_1 \rvert < \infty$$ | Strong consistency: along one infinitely long sample, the average settles at the true mean. |
| **Mean-square** | $$L^2$$ Law of Large Numbers | $$\mathbb{E}[(\bar{X}_n - \mu)^2] = \sigma^2 / n \to 0$$ | Mean squared error of an estimator, and the bias–variance trade-off. |
| **In probability** | Weak Law of Large Numbers | $$\bar{X}_n \xrightarrow{P} \mu$$ | Weak consistency of estimators (MLE, method of moments). Also the engine behind Slutsky's theorem. |
| **In distribution** | Central Limit Theorem (Lindeberg–Lévy) | $$\sqrt{n}(\bar{X}_n - \mu)/\sigma \xrightarrow{d} \mathcal{N}(0, 1)$$ | Approximate confidence intervals and tests, and the Delta Method, without knowing the exact distribution of the data. |

#### Why $$\sqrt{n}$$ appears

Let $$X_1, \dots, X_n$$ be independent with mean $$\mu$$ and variance $$\sigma^2 \in (0, \infty)$$, and let $$S_n = X_1 + \dots + X_n$$. The factor $$\sqrt{n}$$ comes from the fact that variances add up for independent variables.

**Step 1: the sum.** In general

$$
\operatorname{Var}(S_n) = \sum_{i=1}^n \operatorname{Var}(X_i) + 2 \sum_{1 \le i < j \le n} \operatorname{Cov}(X_i, X_j)
$$

Independence makes every covariance zero, so $$\operatorname{Var}(S_n) = n\sigma^2$$ and the standard deviation of the sum grows like $$\sqrt{n}\,\sigma$$.

**Step 2: the average.** Dividing by $$n$$ divides the variance by $$n^2$$:

$$
\operatorname{Var}(\bar{X}_n) = \frac{n\sigma^2}{n^2} = \frac{\sigma^2}{n}, \qquad \operatorname{SE}(\bar{X}_n) = \frac{\sigma}{\sqrt{n}}
$$

**Step 3: finding the right scaling.** Multiply the error $$\bar{X}_n - \mu$$ by $$n^{\gamma}$$ and see what happens to its variance:

$$
\operatorname{Var}\left(n^{\gamma}(\bar{X}_n - \mu)\right) = \sigma^2 \, n^{2\gamma - 1}
$$

* With $$\gamma = 0$$ (no scaling) the variance shrinks to zero and the distribution collapses onto the point $$0$$. This is the LLN.
* With $$\gamma > 1/2$$ (for example $$\gamma = 1$$, the total error $$S_n - n\mu$$) the variance blows up.
* Only $$\gamma = 1/2$$ keeps the variance fixed at $$\sigma^2$$. That is exactly the scaling at which $$\sqrt{n}(\bar{X}_n - \mu)$$ converges to a non-trivial limit, $$\mathcal{N}(0, \sigma^2)$$.

**Step 4: when the assumptions change.**

* With short-range dependence (autocovariances that sum to a finite number $$\sigma_{\text{LR}}^2$$), the variance of $$S_n$$ is still of order $$n$$. The rate stays $$\sqrt{n}$$, but the variance is inflated, which is why HAC (Newey–West) standard errors are used.
* With long-range dependence, $$\operatorname{Cov}(X_t, X_{t+k}) \sim k^{-\alpha}$$ with $$0 < \alpha < 1$$, we get $$\operatorname{Var}(S_n) \sim n^{2-\alpha}$$, and the right normalization becomes $$n^{\alpha/2}$$.
* Some estimators are simply faster. For $$\hat{\theta}_n = \max_i X_i$$ on $$\text{Uniform}(0, \theta)$$, the variance is $$O(n^{-2})$$, so the rate is $$n$$ rather than $$\sqrt{n}$$.
* In practice, since interval widths shrink like $$n^{-1/2}$$, halving the width of a confidence interval requires four times as much data.

---

### 2. Conditional Probability and Bayesian Inference

#### From conditional probability to Bayes' theorem

For events $$A, B \in \mathcal{F}$$ with $$P(B) > 0$$, the conditional probability of $$A$$ given $$B$$ is

$$
P(A \mid B) = \frac{P(A \cap B)}{P(B)}
$$

Since $$P(A \cap B) = P(B \mid A)\,P(A)$$, and using the law of total probability over a partition $$\{A_k\}$$, we get Bayes' theorem:

$$
P(A_k \mid B) = \frac{P(B \mid A_k)\, P(A_k)}{\sum_j P(B \mid A_j)\, P(A_j)}
$$

In parametric inference the data $$\mathcal{D} = (x_1, \dots, x_n)$$ come from a model with an unknown parameter $$\theta$$. If we treat $$\theta$$ as a random quantity with its own distribution, Bayes' theorem turns the model $$p(\mathcal{D} \mid \theta)$$ into a statement about $$\theta$$:

$$
\pi(\theta \mid \mathcal{D}) = \frac{\mathcal{L}(\theta; \mathcal{D})\, \pi(\theta)}{\int_{\Theta} \mathcal{L}(\tilde{\theta}; \mathcal{D})\, \pi(\tilde{\theta})\, d\tilde{\theta}} \;\propto\; \mathcal{L}(\theta; \mathcal{D})\, \pi(\theta)
$$

#### Prior, likelihood, posterior

**Prior** $$\pi(\theta)$$. What we believe about $$\theta$$ before seeing the data: past experience, physical constraints, or a deliberately vague default. It can be informative, weakly informative, or non-informative (for instance the Jeffreys prior $$\pi(\theta) \propto \sqrt{\det \mathcal{I}(\theta)}$$, which does not change under reparametrization).

**Likelihood** $$\mathcal{L}(\theta; \mathcal{D}) = p(\mathcal{D} \mid \theta)$$. For independent observations it is the product $$\prod_i f(x_i \mid \theta)$$. It is read as a function of $$\theta$$ with the data held fixed. It is not a density in $$\theta$$ and does not have to integrate to $$1$$. It is the channel through which the data reweight the prior.

**Posterior** $$\pi(\theta \mid \mathcal{D})$$. The normalized product of prior and likelihood. It contains everything we know about $$\theta$$ after the data, and it gives direct probability statements, such as a credible interval $$P(\theta \in [a, b] \mid \mathcal{D}) = 1 - \alpha$$, or a predictive distribution for a new observation:

$$
p(x_{\text{new}} \mid \mathcal{D}) = \int_{\Theta} p(x_{\text{new}} \mid \theta)\, \pi(\theta \mid \mathcal{D})\, d\theta
$$

#### Updating in stages

Suppose the data arrive in two batches $$\mathcal{D}_1$$ and $$\mathcal{D}_2$$, independent given $$\theta$$. Using the posterior after the first batch as the prior for the second gives

$$
\pi(\theta \mid \mathcal{D}_1, \mathcal{D}_2) \;\propto\; p(\mathcal{D}_2 \mid \theta)\, \pi(\theta \mid \mathcal{D}_1) \;\propto\; p(\mathcal{D}_1, \mathcal{D}_2 \mid \theta)\, \pi(\theta)
$$

So updating batch by batch gives exactly the same result as processing everything at once. This is why Bayesian methods suit streaming data, adaptive clinical trials and anomaly detection.

There is also a link back to Section 1. Under regularity conditions, and with a prior that is positive and smooth around the true value $$\theta_0$$, the Bernstein–von Mises theorem says that the posterior becomes approximately Gaussian, centered at the maximum likelihood estimate and with width of order $$1/\sqrt{n}$$:

$$
\sqrt{n}\left(\theta - \hat{\theta}_{\text{MLE}}\right) \,\Big|\, \mathcal{D} \;\xrightarrow{d}\; \mathcal{N}\left(0,\, \mathcal{I}(\theta_0)^{-1}\right)
$$

With enough data the likelihood outweighs the prior, and the Bayesian and frequentist answers agree.

#### Three worked examples

**Example 1: Beta–Binomial.** We observe $$k$$ successes in $$n$$ Bernoulli trials, so $$\mathcal{L}(\theta; k, n) = \binom{n}{k}\theta^k (1-\theta)^{n-k}$$, and we use the conjugate prior $$\theta \sim \text{Beta}(\alpha_0, \beta_0)$$. The posterior is again a Beta distribution:

$$
\theta \mid k, n \sim \text{Beta}(\alpha_0 + k,\; \beta_0 + n - k)
$$

Its mean is a weighted average of the prior mean $$\mu_0 = \alpha_0 / (\alpha_0 + \beta_0)$$ and the sample proportion $$\hat{p} = k/n$$:

$$
\mathbb{E}[\theta \mid k, n] = \frac{\alpha_0 + \beta_0}{\alpha_0 + \beta_0 + n}\, \mu_0 + \frac{n}{\alpha_0 + \beta_0 + n}\, \hat{p}
$$

The sum $$\kappa_0 = \alpha_0 + \beta_0$$ behaves like a prior sample size: the prior counts as much as $$\kappa_0$$ imaginary observations.

**Example 2: a rare-event alert.** An intrusion detector flags 99% of real attacks, $$P(\text{Alert} \mid \text{Attack}) = 0.99$$, and raises a false alarm on 2% of benign traffic. Only 0.5% of traffic is an attack. If we see an alert, then

$$
P(\text{Attack} \mid \text{Alert}) = \frac{0.99 \times 0.005}{0.99 \times 0.005 + 0.02 \times 0.995} = \frac{0.00495}{0.02485} \approx 0.199
$$

So an alert means an attack only about 20% of the time. Reading the 99% sensitivity as a 99% chance of attack is the base-rate fallacy.

**Example 3: Normal–Normal.** If $$\bar{X}_n \mid \mu \sim \mathcal{N}(\mu, \sigma^2/n)$$ and $$\mu \sim \mathcal{N}(\mu_0, \tau_0^2)$$, the posterior precision is the sum of the two precisions, $$\tau_n^{-2} = \tau_0^{-2} + n/\sigma^2$$. Noisy small-sample estimates get pulled toward $$\mu_0$$. The posterior mode is the same thing as a ridge ($$L_2$$) regularized estimate.

#### Uses, strengths and limits

| | |
| :--- | :--- |
| **Typical uses** | Hierarchical models, A/B tests with early stopping, medical diagnosis, spam and intrusion filtering, Kalman filtering, forecasting with little data. |
| **Strengths** | Uncertainty statements that hold for any sample size, without leaning on the CLT. Prior knowledge enters in a natural way. Sparse data are regularized automatically. Sequential updating is exact. |
| **Limits** | With small $$n$$ the answer depends on the prior. Cromwell's rule: a prior with $$\pi(\theta) = 0$$ on some region can never be overturned by data. In high-dimensional, non-conjugate models the evidence $$p(\mathcal{D})$$ cannot be computed in closed form, so one needs MCMC or variational methods. |
| **Effect on conclusions** | When $$n \ll \kappa_0$$ an informative prior dominates and pulls estimates and intervals toward $$\mu_0$$. When $$n \gg \kappa_0$$ the data dominate, and credible intervals end up close to frequentist confidence intervals. |

---

### 3. Interactive Lab

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);" markdown="0">
    <style>
        .h7-row { display: flex; flex-wrap: wrap; align-items: center; justify-content: center; gap: 0.75rem 1.25rem; margin-bottom: 1rem; }
        .h7-row label { margin: 0; }
        .h7-select { flex: 1 1 280px; max-width: 520px; min-width: 0; background-color: rgba(128,128,128,0.05); color: inherit; }
        .h7-slider { flex: 1 1 200px; max-width: 280px; text-align: center; }
        .h7-slider input[type=range] { width: 100%; }
        .h7-charts { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1.5rem; }
        .h7-chart svg { width: 100%; height: auto; display: block; }
    </style>

    <h5 class="text-center mb-4 font-weight-bold">Convergence and Bayesian Updating</h5>

    <!-- PART A -->
    <div class="px-4 mb-5">
        <h5 class="font-weight-bold text-center mb-3" style="color: #3498db;">A. LLN, CLT and the √n scaling</h5>

        <div class="h7-row">
            <label for="convDistSelect" class="font-weight-bold" style="white-space: nowrap;">Distribution of Xᵢ:</label>
            <select id="convDistSelect" class="form-control h7-select" onchange="runConvergenceLab()">
                <option value="bernoulli">Bernoulli(p = 0.30): μ = 0.30, σ = 0.458</option>
                <option value="exponential">Exponential(λ = 1): μ = 1.00, σ = 1.000</option>
                <option value="uniform">Uniform(0, 1): μ = 0.50, σ = 0.289</option>
                <option value="pareto">Pareto(α = 2.5, xₘ = 1), heavy tail: μ = 1.667, σ = 1.491</option>
            </select>
        </div>

        <div class="h7-row">
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Sample size n: <span id="convNVal" style="color: #3498db;">100</span></label>
                <input type="range" id="convNRange" class="custom-range" min="10" max="500" step="10" value="100" oninput="document.getElementById('convNVal').innerText = this.value; runConvergenceLab();">
            </div>
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Monte Carlo trials M: <span id="convMVal" style="color: #9b59b6;">400</span></label>
                <input type="range" id="convMRange" class="custom-range" min="100" max="1000" step="100" value="400" oninput="document.getElementById('convMVal').innerText = this.value; runConvergenceLab();">
            </div>
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Tolerance ε: <span id="convEpsVal" style="color: #e67e22;">0.10</span></label>
                <input type="range" id="convEpsRange" class="custom-range" min="0.02" max="0.30" step="0.02" value="0.10" oninput="document.getElementById('convEpsVal').innerText = parseFloat(this.value).toFixed(2); runConvergenceLab();">
            </div>
        </div>

        <div class="h7-row">
            <button type="button" class="btn btn-sm btn-outline-primary px-4" onclick="runConvergenceLab()">Run a new simulation</button>
        </div>

        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th class="text-center">Mean μ</th>
                        <th class="text-center">Standard error σ/√n</th>
                        <th class="text-center">Simulated E[(X̄ₙ − μ)²]</th>
                        <th class="text-center">Theory σ²/n</th>
                        <th class="text-center">P(|X̄ₙ − μ| ≥ ε)</th>
                        <th class="text-center">SD of √n(X̄ₙ − μ)/σ</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td id="convTrueMean" class="font-weight-bold text-center">-</td>
                        <td id="convSE" class="text-center" style="color: #3498db; font-weight: bold;">-</td>
                        <td id="convEmpL2" class="text-center" style="color: #2ecc71; font-weight: bold;">-</td>
                        <td id="convTheoL2" class="text-center">-</td>
                        <td id="convTailProb" class="text-center" style="color: #e67e22; font-weight: bold;">-</td>
                        <td id="convCltSd" class="text-center" style="color: #9b59b6; font-weight: bold;">-</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="h7-charts mt-4">
            <div>
                <h6 class="font-weight-bold text-center mb-3">Running averages X̄ₖ and the ±1.96σ/√k band</h6>
                <div id="convPathsChart" class="h7-chart"></div>
            </div>
            <div>
                <h6 class="font-weight-bold text-center mb-3">CLT: raw error vs. √n-scaled error</h6>
                <div id="convCltChart" class="h7-chart"></div>
            </div>
        </div>
    </div>

    <hr class="w-75 mx-auto mb-5" style="border-color: rgba(150,150,150,0.25);">

    <!-- PART B -->
    <div class="px-4 mb-2">
        <h5 class="font-weight-bold text-center mb-3" style="color: #9b59b6;">B. Bayesian updating (Beta–Binomial)</h5>

        <div class="h7-row">
            <label for="bayesPriorPreset" class="font-weight-bold" style="white-space: nowrap;">Prior π(θ):</label>
            <select id="bayesPriorPreset" class="form-control h7-select" onchange="onBayesPresetChange()">
                <option value="uniform">Uniform: Beta(1, 1)</option>
                <option value="jeffreys">Jeffreys: Beta(0.5, 0.5)</option>
                <option value="skeptical">Skeptical, low rate: Beta(2, 18), mean 0.10</option>
                <option value="informative">Strong and centered: Beta(15, 15), mean 0.50</option>
            </select>
        </div>

        <div class="h7-row">
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Prior α₀: <span id="bayesAlphaVal" style="color: #3498db;">1.0</span></label>
                <input type="range" id="bayesAlphaRange" class="custom-range" min="0.5" max="30" step="0.5" value="1" oninput="document.getElementById('bayesAlphaVal').innerText = parseFloat(this.value).toFixed(1); runBayesianLab();">
            </div>
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Prior β₀: <span id="bayesBetaVal" style="color: #3498db;">1.0</span></label>
                <input type="range" id="bayesBetaRange" class="custom-range" min="0.5" max="30" step="0.5" value="1" oninput="document.getElementById('bayesBetaVal').innerText = parseFloat(this.value).toFixed(1); runBayesianLab();">
            </div>
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Trials n: <span id="bayesNVal" style="color: #2ecc71;">25</span></label>
                <input type="range" id="bayesNRange" class="custom-range" min="1" max="200" step="1" value="25" oninput="syncSuccessSlider(); runBayesianLab();">
            </div>
            <div class="h7-slider">
                <label class="font-weight-bold d-block">Successes k: <span id="bayesKVal" style="color: #e74c3c;">18</span></label>
                <input type="range" id="bayesKRange" class="custom-range" min="0" max="25" step="1" value="18" oninput="document.getElementById('bayesKVal').innerText = this.value; runBayesianLab();">
            </div>
        </div>

        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th class="text-center">Prior mean</th>
                        <th class="text-center">MLE k/n</th>
                        <th class="text-center">Posterior</th>
                        <th class="text-center">Posterior mean</th>
                        <th class="text-center">Posterior mode (MAP)</th>
                        <th class="text-center">95% credible interval</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td id="bayesPriorMean" class="text-center" style="color: #3498db; font-weight: bold;">-</td>
                        <td id="bayesMle" class="text-center" style="color: #2ecc71; font-weight: bold;">-</td>
                        <td id="bayesPostParams" class="text-center">-</td>
                        <td id="bayesPostMean" class="text-center" style="color: #9b59b6; font-weight: bold;">-</td>
                        <td id="bayesPostMap" class="text-center">-</td>
                        <td id="bayesPostCI" class="font-weight-bold text-center">-</td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="row justify-content-center mt-4">
            <div class="col-lg-10">
                <h6 class="font-weight-bold text-center mb-3">Prior, likelihood and posterior</h6>
                <div id="bayesUpdateChart" class="h7-chart"></div>
            </div>
        </div>

        <div class="mt-3">
            <div id="bayesInsightBox" class="p-3 rounded text-center" style="background-color: rgba(128,128,128,0.07); border-top: 3px solid #9b59b6;">
                Loading...
            </div>
        </div>
    </div>
</div>

<!-- Scripts -->
{% raw %}
<script>
// ---------- Shared helpers ----------

const H7 = { W: 460, H: 300, L: 54, R: 12, T: 12, B: 84 };
H7.pw = H7.W - H7.L - H7.R;
H7.ph = H7.H - H7.T - H7.B;

function h7Num(v) { return String(+Number(v).toPrecision(3)); }

function h7Esc(s) {
    return String(s).replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
}

function h7Ticks(lo, hi, count) {
    const raw = (hi - lo) / count;
    if (!(raw > 0)) return [lo];
    const p = Math.pow(10, Math.floor(Math.log10(raw)));
    const f = raw / p;
    const step = (f < 1.5 ? 1 : f < 3 ? 2 : f < 7 ? 5 : 10) * p;
    const out = [];
    for (let v = Math.ceil(lo / step - 1e-9) * step; v <= hi + step * 1e-9; v += step) out.push(v);
    return out;
}

function h7Open(clipId) {
    return `<svg viewBox="0 0 ${H7.W} ${H7.H}" xmlns="http://www.w3.org/2000/svg" role="img">` +
        `<defs><clipPath id="${clipId}"><rect x="${H7.L}" y="${H7.T}" width="${H7.pw}" height="${H7.ph}"/></clipPath></defs>`;
}

function h7Axes(sx, sy, xt, yt, xLabel, yLabel) {
    const grid = 'stroke="currentColor" stroke-opacity="0.12"';
    const axis = 'stroke="currentColor" stroke-opacity="0.45"';
    const bottom = H7.T + H7.ph;
    let s = "";
    xt.forEach(v => {
        const x = sx(v).toFixed(1);
        s += `<line x1="${x}" y1="${H7.T}" x2="${x}" y2="${bottom}" ${grid}/>`;
        s += `<text x="${x}" y="${bottom + 14}" fill="currentColor" font-size="10" text-anchor="middle">${h7Num(v)}</text>`;
    });
    yt.forEach(v => {
        const y = sy(v).toFixed(1);
        s += `<line x1="${H7.L}" y1="${y}" x2="${H7.W - H7.R}" y2="${y}" ${grid}/>`;
        s += `<text x="${H7.L - 6}" y="${(+y + 3).toFixed(1)}" fill="currentColor" font-size="10" text-anchor="end">${h7Num(v)}</text>`;
    });
    s += `<line x1="${H7.L}" y1="${H7.T}" x2="${H7.L}" y2="${bottom}" ${axis}/>`;
    s += `<line x1="${H7.L}" y1="${bottom}" x2="${H7.W - H7.R}" y2="${bottom}" ${axis}/>`;
    s += `<text x="${H7.L + H7.pw / 2}" y="${bottom + 30}" fill="currentColor" font-size="10" text-anchor="middle">${h7Esc(xLabel)}</text>`;
    s += `<text x="12" y="${H7.T + H7.ph / 2}" fill="currentColor" font-size="10" text-anchor="middle" transform="rotate(-90 12,${H7.T + H7.ph / 2})">${h7Esc(yLabel)}</text>`;
    return s;
}

// items: [color, text, "line" | "box"]
function h7Legend(items) {
    let s = "";
    items.forEach((it, k) => {
        const y = H7.T + H7.ph + 46 + k * 13;
        if (it[2] === "line") {
            s += `<line x1="${H7.L}" y1="${y - 3}" x2="${H7.L + 12}" y2="${y - 3}" stroke="${it[0]}" stroke-width="2.5"/>`;
        } else {
            s += `<rect x="${H7.L}" y="${y - 8}" width="12" height="9" fill="${it[0]}" fill-opacity="0.6"/>`;
        }
        s += `<text x="${H7.L + 18}" y="${y}" fill="currentColor" font-size="10">${h7Esc(it[1])}</text>`;
    });
    return s;
}

function h7Poly(points, attrs) {
    return `<polyline points="${points.map(p => p[0].toFixed(1) + "," + p[1].toFixed(1)).join(" ")}" fill="none" ${attrs}/>`;
}

// ---------- PART A: convergence ----------

function getParentDistribution(type) {
    switch (type) {
        case "bernoulli": {
            const p = 0.30;
            return { mean: p, sd: Math.sqrt(p * (1 - p)), sample: () => (Math.random() < p ? 1.0 : 0.0) };
        }
        case "exponential":
            return { mean: 1.0, sd: 1.0, sample: () => -Math.log(1.0 - Math.random()) };
        case "uniform":
            return { mean: 0.5, sd: Math.sqrt(1.0 / 12.0), sample: () => Math.random() };
        case "pareto": {
            const alpha = 2.5, xm = 1.0;
            const mean = (alpha * xm) / (alpha - 1.0);
            const variance = (xm * xm * alpha) / (Math.pow(alpha - 1.0, 2) * (alpha - 2.0));
            return { mean: mean, sd: Math.sqrt(variance), sample: () => xm / Math.pow(1.0 - Math.random(), 1.0 / alpha) };
        }
        default:
            return { mean: 0.5, sd: Math.sqrt(1 / 12), sample: () => Math.random() };
    }
}

function runConvergenceLab() {
    const distEl = document.getElementById("convDistSelect");
    if (!distEl) return;

    const n = parseInt(document.getElementById("convNRange").value, 10);
    const M = parseInt(document.getElementById("convMRange").value, 10);
    const eps = parseFloat(document.getElementById("convEpsRange").value);

    const dist = getParentDistribution(distEl.value);
    const mu = dist.mean, sigma = dist.sd;
    const theoSE = sigma / Math.sqrt(n);
    const theoL2 = (sigma * sigma) / n;

    const numPaths = 8;
    const paths = [];
    const unscaled = new Array(M);
    const scaled = new Array(M);
    let sqErrSum = 0.0, tailCount = 0;

    for (let m = 0; m < M; m++) {
        let sum = 0.0;
        const path = m < numPaths ? new Array(n) : null;
        for (let i = 0; i < n; i++) {
            sum += dist.sample();
            if (path) path[i] = sum / (i + 1);
        }
        if (path) paths.push(path);
        const err = sum / n - mu;
        unscaled[m] = err;
        scaled[m] = (Math.sqrt(n) * err) / sigma;
        sqErrSum += err * err;
        if (Math.abs(err) >= eps) tailCount++;
    }

    const zMean = scaled.reduce((a, b) => a + b, 0) / M;
    const zVar = scaled.reduce((a, b) => a + (b - zMean) * (b - zMean), 0) / M;

    document.getElementById("convTrueMean").innerText = mu.toFixed(4);
    document.getElementById("convSE").innerText = theoSE.toFixed(4);
    document.getElementById("convEmpL2").innerText = (sqErrSum / M).toExponential(2);
    document.getElementById("convTheoL2").innerText = theoL2.toExponential(2);
    document.getElementById("convTailProb").innerText = (tailCount / M).toFixed(4);
    document.getElementById("convCltSd").innerText = Math.sqrt(zVar).toFixed(3);

    h7DrawPaths(n, mu, sigma, paths);
    h7DrawClt(scaled, unscaled);
}

function h7DrawPaths(n, mu, sigma, paths) {
    // y-range: ignore the very first (wild) steps; those are clipped
    const kRef = Math.min(n, 8);
    let lo = mu - 1.96 * sigma / Math.sqrt(kRef);
    let hi = mu + 1.96 * sigma / Math.sqrt(kRef);
    paths.forEach(p => {
        for (let k = kRef; k <= n; k++) {
            if (p[k - 1] < lo) lo = p[k - 1];
            if (p[k - 1] > hi) hi = p[k - 1];
        }
    });
    const pad = (hi - lo) * 0.05;
    lo -= pad; hi += pad;

    const sx = k => H7.L + ((k - 1) / Math.max(1, n - 1)) * H7.pw;
    const sy = y => H7.T + H7.ph - ((y - lo) / (hi - lo)) * H7.ph;

    const step = Math.max(1, Math.floor(n / 150));
    const ks = [];
    for (let k = 1; k <= n; k += step) ks.push(k);
    if (ks[ks.length - 1] !== n) ks.push(n);

    const colors = ["#3498db", "#9b59b6", "#2ecc71", "#e67e22", "#1abc9c", "#f1c40f", "#e84393", "#00cec9"];

    let s = h7Open("h7clipA");
    s += h7Axes(sx, sy, h7Ticks(1, n, 5), h7Ticks(lo, hi, 5), "Sample size k", "Running average");
    s += `<g clip-path="url(#h7clipA)">`;
    paths.forEach((p, i) => {
        s += h7Poly(ks.map(k => [sx(k), sy(p[k - 1])]), `stroke="${colors[i % colors.length]}" stroke-width="1.3" stroke-opacity="0.9"`);
    });
    s += h7Poly(ks.map(k => [sx(k), sy(mu + 1.96 * sigma / Math.sqrt(k))]), `stroke="currentColor" stroke-opacity="0.55" stroke-width="1.3" stroke-dasharray="4,3"`);
    s += h7Poly(ks.map(k => [sx(k), sy(mu - 1.96 * sigma / Math.sqrt(k))]), `stroke="currentColor" stroke-opacity="0.55" stroke-width="1.3" stroke-dasharray="4,3"`);
    s += `<line x1="${H7.L}" y1="${sy(mu).toFixed(1)}" x2="${H7.W - H7.R}" y2="${sy(mu).toFixed(1)}" stroke="#e74c3c" stroke-width="2" stroke-dasharray="6,4"/>`;
    s += `</g>`;
    s += h7Legend([
        ["#3498db", "8 simulated sample paths", "line"],
        ["#e74c3c", "True mean μ", "line"],
        ["#999999", "μ ± 1.96σ/√k (early steps are clipped)", "line"]
    ]);
    s += `</svg>`;
    document.getElementById("convPathsChart").innerHTML = s;
}

function h7DrawClt(scaled, unscaled) {
    const nb = 28, lo = -3.5, hi = 3.5, bw = (hi - lo) / nb, M = scaled.length;
    const hz = new Array(nb).fill(0), hu = new Array(nb).fill(0);
    for (let m = 0; m < M; m++) {
        let b = Math.floor((scaled[m] - lo) / bw);
        if (b >= 0 && b < nb) hz[b]++;
        b = Math.floor((unscaled[m] - lo) / bw);
        if (b >= 0 && b < nb) hu[b]++;
    }
    for (let b = 0; b < nb; b++) { hz[b] /= (M * bw); hu[b] /= (M * bw); }

    const normal = x => Math.exp(-0.5 * x * x) / Math.sqrt(2 * Math.PI);
    const yTop = Math.max(0.3989, Math.max.apply(null, hz)) * 1.2;
    const sx = x => H7.L + ((x - lo) / (hi - lo)) * H7.pw;
    const sy = y => H7.T + H7.ph - (y / yTop) * H7.ph;
    const base = sy(0);

    let s = h7Open("h7clipB");
    s += h7Axes(sx, sy, h7Ticks(lo, hi, 7), h7Ticks(0, yTop, 5), "Value", "Density");
    s += `<g clip-path="url(#h7clipB)">`;
    for (let b = 0; b < nb; b++) {
        const x = sx(lo + b * bw);
        const w = (H7.pw / nb) - 1;
        s += `<rect x="${x.toFixed(1)}" y="${sy(hz[b]).toFixed(1)}" width="${w.toFixed(1)}" height="${Math.max(0, base - sy(hz[b])).toFixed(1)}" fill="#3498db" fill-opacity="0.5"/>`;
        if (hu[b] > 0) {
            s += `<rect x="${x.toFixed(1)}" y="${sy(hu[b]).toFixed(1)}" width="${w.toFixed(1)}" height="${Math.max(0, base - sy(hu[b])).toFixed(1)}" fill="#9b59b6" fill-opacity="0.45" stroke="#9b59b6" stroke-width="1"/>`;
        }
    }
    const curve = [];
    for (let i = 0; i <= 140; i++) {
        const x = lo + (i / 140) * (hi - lo);
        curve.push([sx(x), sy(normal(x))]);
    }
    s += h7Poly(curve, `stroke="#e74c3c" stroke-width="2.2"`);
    s += `</g>`;
    s += h7Legend([
        ["#3498db", "√n (X̄ₙ − μ) / σ", "box"],
        ["#9b59b6", "X̄ₙ − μ, unscaled (bars run off the top: all mass sits near 0)", "box"],
        ["#e74c3c", "Standard normal N(0, 1)", "line"]
    ]);
    s += `</svg>`;
    document.getElementById("convCltChart").innerHTML = s;
}

// ---------- PART B: Beta-Binomial ----------

function h7LogGamma(z) {
    const c = [76.18009172947146, -86.50532032941677, 24.01409824083091,
               -1.231739572450155, 0.1208650973866179e-2, -0.5395239384953e-5];
    let y = z;
    let tmp = z + 5.5;
    tmp -= (z + 0.5) * Math.log(tmp);
    let ser = 1.000000000190015;
    for (let j = 0; j < 6; j++) { y += 1; ser += c[j] / y; }
    return -tmp + Math.log(2.5066282746310005 * ser / z);
}

function betaPdf(x, a, b) {
    if (x <= 0.0 || x >= 1.0) return 0.0;
    const logB = h7LogGamma(a) + h7LogGamma(b) - h7LogGamma(a + b);
    return Math.exp((a - 1.0) * Math.log(x) + (b - 1.0) * Math.log(1.0 - x) - logB);
}

// Regularized incomplete beta function I_x(a, b) (continued fraction)
function betaCF(x, a, b) {
    const FPMIN = 1e-300;
    const qab = a + b, qap = a + 1, qam = a - 1;
    let c = 1, d = 1 - qab * x / qap;
    if (Math.abs(d) < FPMIN) d = FPMIN;
    d = 1 / d;
    let h = d;
    for (let m = 1; m <= 300; m++) {
        const m2 = 2 * m;
        let aa = m * (b - m) * x / ((qam + m2) * (a + m2));
        d = 1 + aa * d; if (Math.abs(d) < FPMIN) d = FPMIN;
        c = 1 + aa / c; if (Math.abs(c) < FPMIN) c = FPMIN;
        d = 1 / d; h *= d * c;
        aa = -(a + m) * (qab + m) * x / ((a + m2) * (qap + m2));
        d = 1 + aa * d; if (Math.abs(d) < FPMIN) d = FPMIN;
        c = 1 + aa / c; if (Math.abs(c) < FPMIN) c = FPMIN;
        d = 1 / d;
        const del = d * c;
        h *= del;
        if (Math.abs(del - 1) < 3e-14) break;
    }
    return h;
}

function betaInc(x, a, b) {
    if (x <= 0) return 0;
    if (x >= 1) return 1;
    const bt = Math.exp(h7LogGamma(a + b) - h7LogGamma(a) - h7LogGamma(b) + a * Math.log(x) + b * Math.log(1 - x));
    if (x < (a + 1) / (a + b + 2)) return bt * betaCF(x, a, b) / a;
    return 1 - bt * betaCF(1 - x, b, a) / b;
}

function betaQuantile(p, a, b) {
    let lo = 0, hi = 1;
    for (let i = 0; i < 60; i++) {
        const mid = 0.5 * (lo + hi);
        if (betaInc(mid, a, b) < p) lo = mid; else hi = mid;
    }
    return 0.5 * (lo + hi);
}

let h7LastN = 25;

function onBayesPresetChange() {
    const preset = document.getElementById("bayesPriorPreset").value;
    let a0 = 1.0, b0 = 1.0;
    if (preset === "jeffreys") { a0 = 0.5; b0 = 0.5; }
    else if (preset === "skeptical") { a0 = 2.0; b0 = 18.0; }
    else if (preset === "informative") { a0 = 15.0; b0 = 15.0; }

    document.getElementById("bayesAlphaRange").value = a0;
    document.getElementById("bayesBetaRange").value = b0;
    document.getElementById("bayesAlphaVal").innerText = a0.toFixed(1);
    document.getElementById("bayesBetaVal").innerText = b0.toFixed(1);
    runBayesianLab();
}

// When n changes, keep the observed proportion k/n roughly fixed
function syncSuccessSlider() {
    const n = parseInt(document.getElementById("bayesNRange").value, 10);
    document.getElementById("bayesNVal").innerText = n;
    const kSlider = document.getElementById("bayesKRange");
    const prop = parseInt(kSlider.value, 10) / h7LastN;
    kSlider.max = n;
    const newK = Math.min(n, Math.round(prop * n));
    kSlider.value = newK;
    document.getElementById("bayesKVal").innerText = newK;
    h7LastN = n;
}

function runBayesianLab() {
    const aEl = document.getElementById("bayesAlphaRange");
    if (!aEl) return;

    const a0 = parseFloat(aEl.value);
    const b0 = parseFloat(document.getElementById("bayesBetaRange").value);
    const n = parseInt(document.getElementById("bayesNRange").value, 10);
    const k = Math.min(n, parseInt(document.getElementById("bayesKRange").value, 10));

    const aPost = a0 + k;
    const bPost = b0 + (n - k);
    const priorMean = a0 / (a0 + b0);
    const mle = k / n;
    const postMean = aPost / (aPost + bPost);

    let postMode;
    if (aPost > 1 && bPost > 1) postMode = ((aPost - 1) / (aPost + bPost - 2)).toFixed(4);
    else if (aPost <= 1 && bPost > 1) postMode = "0 (boundary)";
    else if (bPost <= 1 && aPost > 1) postMode = "1 (boundary)";
    else postMode = "bimodal (0 and 1)";

    const ciLow = betaQuantile(0.025, aPost, bPost);
    const ciHigh = betaQuantile(0.975, aPost, bPost);

    document.getElementById("bayesPriorMean").innerText = priorMean.toFixed(4);
    document.getElementById("bayesMle").innerText = mle.toFixed(4);
    document.getElementById("bayesPostParams").innerText = `Beta(${aPost.toFixed(1)}, ${bPost.toFixed(1)})`;
    document.getElementById("bayesPostMean").innerText = postMean.toFixed(4);
    document.getElementById("bayesPostMap").innerText = postMode;
    document.getElementById("bayesPostCI").innerText = `[${ciLow.toFixed(3)}, ${ciHigh.toFixed(3)}]`;

    const priorWeight = (a0 + b0) / (a0 + b0 + n);
    const box = document.getElementById("bayesInsightBox");
    if (box) {
        box.innerHTML = `<strong>How the posterior mean is built:</strong> E[θ | D] = ${postMean.toFixed(4)} is <strong>${(priorWeight * 100).toFixed(1)}%</strong> prior mean (${priorMean.toFixed(4)}) and <strong>${((1 - priorWeight) * 100).toFixed(1)}%</strong> sample proportion (${mle.toFixed(4)}). Increase <em>n</em> and the data weight goes to 100%, so the posterior narrows around the MLE.`;
    }

    h7DrawBayes(a0, b0, k, n, aPost, bPost, ciLow, ciHigh);
}

function h7DrawBayes(a0, b0, k, n, aPost, bPost, ciLow, ciHigh) {
    const N = 300;
    const th = [], pr = [], li = [], po = [];
    let peak = 1.2;
    for (let i = 1; i < N; i++) {
        const t = i / N;
        th.push(t);
        pr.push(betaPdf(t, a0, b0));
        li.push(betaPdf(t, k + 1, n - k + 1));  // likelihood normalized to area 1
        po.push(betaPdf(t, aPost, bPost));
    }
    po.forEach(v => { if (isFinite(v) && v > peak) peak = v; });
    li.forEach(v => { if (isFinite(v) && v > peak) peak = v; });
    const yTop = peak * 1.12;

    const sx = t => H7.L + t * H7.pw;
    const sy = y => H7.T + H7.ph - (y / yTop) * H7.ph;
    const line = arr => th.map((t, i) => [sx(t), sy(isFinite(arr[i]) ? arr[i] : 0)]);

    const postPts = line(po);
    const area = `M ${sx(th[0]).toFixed(1)} ${sy(0).toFixed(1)} ` +
        postPts.map(p => `L ${p[0].toFixed(1)} ${p[1].toFixed(1)}`).join(" ") +
        ` L ${sx(th[th.length - 1]).toFixed(1)} ${sy(0).toFixed(1)} Z`;

    let s = h7Open("h7clipC");
    s += h7Axes(sx, sy, h7Ticks(0, 1, 5), h7Ticks(0, yTop, 5), "Parameter θ", "Density");
    s += `<g clip-path="url(#h7clipC)">`;
    s += `<path d="${area}" fill="#9b59b6" fill-opacity="0.2"/>`;
    s += h7Poly(line(pr), `stroke="#3498db" stroke-width="2" stroke-dasharray="6,4"`);
    s += h7Poly(line(li), `stroke="#2ecc71" stroke-width="2"`);
    s += h7Poly(postPts, `stroke="#9b59b6" stroke-width="2.6"`);
    s += `<line x1="${sx(ciLow).toFixed(1)}" y1="${sy(0) - 3}" x2="${sx(ciHigh).toFixed(1)}" y2="${sy(0) - 3}" stroke="#9b59b6" stroke-width="5" stroke-linecap="round"/>`;
    s += `</g>`;
    s += h7Legend([
        ["#3498db", "Prior π(θ)", "line"],
        ["#2ecc71", "Likelihood of the data (rescaled to area 1)", "line"],
        ["#9b59b6", "Posterior π(θ | k, n), thick bar = 95% credible interval", "line"]
    ]);
    s += `</svg>`;
    document.getElementById("bayesUpdateChart").innerHTML = s;
}

// ---------- Startup ----------

function startHomework7() {
    if (!document.getElementById("convPathsChart") || !document.getElementById("bayesUpdateChart")) {
        setTimeout(startHomework7, 50);
        return;
    }
    runConvergenceLab();
    runBayesianLab();
}

if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", startHomework7);
} else {
    startHomework7();
}
</script>
{% endraw %}
