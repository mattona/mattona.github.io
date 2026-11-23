---
layout: page
title: Modes of Convergence & Bayesian Statistical Inference
description: Asymptotic convergence hierarchy in probability (LLN, CLT, and the √n scaling law) alongside Bayesian sequential updating via Prior, Likelihood, and Posterior distributions.
img: assets/img/hmw7.jpg
importance: 7
category: Statistics
permalink: /projects/homework7/
giscus_comments: true
---

This assignment explores two foundational pillars of mathematical statistics and probabilistic reasoning:
1. **Asymptotic Theory & Modes of Convergence:** Contrasting almost sure, mean-square ($$L^2$$), in-probability, and weak (in-distribution) convergence; establishing their hierarchy and roles in the **Law of Large Numbers (LLN)** and **Central Limit Theorem (CLT)**; and proving why the **$$\sqrt{n}$$** scaling rate arises from variance additivity under independence.
2. **Conditional Probability & Bayesian Inference:** Formalizing Bayes' theorem as a coherent belief-updating mechanism that combines **Prior**, **Likelihood**, and **Posterior** distributions, illustrated through analytical conjugate models, diagnostic base-rate reasoning, and a critical evaluation of prior sensitivity.

---

### 1. Modes of Convergence in Probability Theory

#### Mathematical Definitions of the Four Principal Modes

Let $$(\Omega, \mathcal{F}, P)$$ be a probability space, let $$\{X_n\}_{n \ge 1}$$ be a sequence of real-valued random variables, and let $$X$$ be a limiting random variable defined on the same space (except for convergence in distribution, which requires only marginal laws).

1. **Almost Sure Convergence (Strong Convergence, $$X_n \xrightarrow{\text{a.s.}} X$$):**
   The sequence converges pointwise to $$X$$ on an event of probability one:
   $$
   P\left(\left\{\omega \in \Omega : \lim_{n \to \infty} X_n(\omega) = X(\omega)\right\}\right) = 1
   $$
   Equivalently, for every $$\varepsilon > 0$$, $$\lim_{m \to \infty} P\left(\sup_{n \ge m} |X_n - X| \ge \varepsilon\right) = 0$$.

2. **Mean-Square Convergence (Quadratic Mean / $$L^2$$ Convergence, $$X_n \xrightarrow{L^2} X$$):**
   Assuming $$\mathbb{E}[X_n^2] < \infty$$ and $$\mathbb{E}[X^2] < \infty$$, the expected squared error vanishes:
   $$
   \lim_{n \to \infty} \mathbb{E}\left[(X_n - X)^2\right] = 0
   $$
   When $$X = \theta$$ is a deterministic parameter, the $$L^2$$ error decomposes into variance plus squared bias: $$\mathbb{E}[(X_n - \theta)^2] = \text{Var}(X_n) + \left(\mathbb{E}[X_n] - \theta\right)^2$$.

3. **Convergence in Probability (Weak Convergence in Measure, $$X_n \xrightarrow{P} X$$):**
   For every tolerance threshold $$\varepsilon > 0$$, the probability of observing a deviation exceeding $$\varepsilon$$ tends to zero:
   $$
   \forall \varepsilon > 0, \qquad \lim_{n \to \infty} P\left(|X_n - X| \ge \varepsilon\right) = 0
   $$
   Unlike almost sure convergence, $$X_n \xrightarrow{P} X$$ permits rare outlier spikes to recur infinitely often along individual sample paths, provided their instantaneous probability decays to zero.

4. **Convergence in Distribution (Weak Convergence / Convergence in Law, $$X_n \xrightarrow{d} X$$):**
   Let $$F_{X_n}(x) = P(X_n \le x)$$ and $$F_X(x) = P(X \le x)$$ denote the cumulative distribution functions (CDFs). Then $$X_n \xrightarrow{d} X$$ if:
   $$
   \lim_{n \to \infty} F_{X_n}(x) = F_X(x) \qquad \text{for every continuity point } x \text{ of } F_X
   $$
   This is a property solely of the marginal probability laws rather than joint coupling on $$\Omega$$.

<div class="my-4 p-4 rounded z-depth-1" style="background-color: rgba(128,128,128,0.03); border: 1px solid rgba(150,150,150,0.25);" markdown="0">
    <div class="text-center mb-3">
        <span class="badge badge-primary px-3 py-2 font-weight-bold" style="background-color: #3498db; font-size: 0.85rem;">
            HIERARCHY OF STOCHASTIC CONVERGENCE IMPLICATION GRAPH
        </span>
        <p class="small text-muted mt-2 mb-0">Solid arrows indicate unconditional implications; dashed arrows require supplementary regularity conditions.</p>
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

        <!-- Node 1: Almost Sure -->
        <rect x="20" y="20" width="190" height="62" rx="8" fill="#9b59b6" fill-opacity="0.15" stroke="#9b59b6" stroke-width="2"/>
        <text x="115" y="46" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">Almost Sure (a.s.)</text>
        <text x="115" y="66" fill="#9b59b6" font-size="11" font-weight="bold" text-anchor="middle">P(lim Xₙ = X) = 1</text>

        <!-- Node 2: L2 Mean Square -->
        <rect x="20" y="130" width="190" height="62" rx="8" fill="#2ecc71" fill-opacity="0.15" stroke="#2ecc71" stroke-width="2"/>
        <text x="115" y="156" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">Mean-Square (L²)</text>
        <text x="115" y="176" fill="#2ecc71" font-size="11" font-weight="bold" text-anchor="middle">E[(Xₙ − X)²] → 0</text>

        <!-- Node 3: In Probability -->
        <rect x="295" y="75" width="190" height="62" rx="8" fill="#3498db" fill-opacity="0.18" stroke="#3498db" stroke-width="2"/>
        <text x="390" y="101" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">In Probability (P)</text>
        <text x="390" y="121" fill="#3498db" font-size="11" font-weight="bold" text-anchor="middle">P(|Xₙ − X| ≥ ε) → 0</text>

        <!-- Node 4: In Distribution -->
        <rect x="555" y="75" width="185" height="62" rx="8" fill="#e74c3c" fill-opacity="0.15" stroke="#e74c3c" stroke-width="2"/>
        <text x="647" y="101" fill="currentColor" font-size="13" font-weight="bold" text-anchor="middle">In Distribution (d)</text>
        <text x="647" y="121" fill="#e74c3c" font-size="11" font-weight="bold" text-anchor="middle">Fₙ(x) → F(x)</text>

        <!-- Solid Arrow: a.s. -> P -->
        <line x1="210" y1="55" x2="292" y2="95" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="248" y="64" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">Fatou / Borel-Cantelli</text>

        <!-- Solid Arrow: L2 -> P -->
        <line x1="210" y1="158" x2="292" y2="118" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="252" y="153" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">Chebyshev Ineq.</text>

        <!-- Solid Arrow: P -> d -->
        <line x1="485" y1="98" x2="552" y2="98" stroke="#3498db" stroke-width="2" marker-end="url(#arrowSolid)"/>
        <text x="518" y="88" fill="#3498db" font-size="10" font-weight="bold" text-anchor="middle">Always</text>

        <!-- Dashed Converse: d -> P when X = c -->
        <line x1="555" y1="118" x2="488" y2="118" stroke="#e67e22" stroke-width="1.8" stroke-dasharray="4,3" marker-end="url(#arrowDashed)"/>
        <text x="520" y="135" fill="#e67e22" font-size="10" font-weight="bold" text-anchor="middle">If X = c (const)</text>

        <!-- Dashed Converse: P -> a.s. -->
        <path d="M 320 75 Q 260 22, 213 38" fill="none" stroke="#e67e22" stroke-width="1.8" stroke-dasharray="4,3" marker-end="url(#arrowDashed)"/>
        <text x="265" y="18" fill="#e67e22" font-size="10" font-weight="bold" text-anchor="middle">Fast rate: ∑ P(|Xₙ−X|≥ε) &lt; ∞</text>
    </svg>
</div>

#### Roles in the Law of Large Numbers, Central Limit Theorem & Statistical Inference

| Mode of Convergence | Core Limit Theorem | Canonical Statement | Role in Statistical Inference |
| :--- | :--- | :--- | :--- |
| **Almost Sure ($$\xrightarrow{\text{a.s.}}$$)** | **Strong Law of Large Numbers (SLLN)** (Kolmogorov) | $$\bar{X}_n = \frac{1}{n}\sum_{i=1}^n X_i \xrightarrow{\text{a.s.}} \mu$$ iff $$\mathbb{E}[|X_1|] < \infty$$ | Guarantees **strong consistency**: with probability $$1$$, a single infinitely extended sample trajectory stabilizes at the true parameter $$\theta_0$$. |
| **Mean-Square ($$\xrightarrow{L^2}$$)** | **$$L^2$$ Law of Large Numbers** | $$\mathbb{E}[(\bar{X}_n - \mu)^2] = \frac{\sigma^2}{n} \to 0$$ | Evaluates **Mean Squared Error (MSE)** efficiency, governs unbiasedness-variance trade-offs, and establishes consistency of minimum-variance estimators. |
| **In Probability ($$\xrightarrow{P}$$)** | **Weak Law of Large Numbers (WLLN)** (Khinchin) | $$\bar{X}_n \xrightarrow{P} \mu$$ | Defines **weak consistency** ($$\hat{\theta}_n \xrightarrow{P} \theta_0$$) for MLE, Method of Moments, and empirical risk minimizers; powers **Slutsky's Theorem**. |
| **In Distribution ($$\xrightarrow{d}$$)** | **Central Limit Theorem (CLT)** (Lindeberg–Lévy) | $$\frac{\sqrt{n}(\bar{X}_n - \mu)}{\sigma} \xrightarrow{d} \mathcal{N}(0, 1)$$ | Provides **asymptotic normality** for approximate confidence intervals, Wald/Score/LRT hypothesis tests, and the **Delta Method** without knowing the exact parent distribution. |

#### Why the Term $$\sqrt{n}$$ Appears in Convergence Theorems

The ubiquity of $$\sqrt{n}$$ in asymptotic statistics is a direct algebraic consequence of the **additivity of variance** for independent (or uncorrelated) random variables. Let $$X_1, X_2, \dots, X_n$$ be independent observations with mean $$\mathbb{E}[X_i] = \mu$$ and finite variance $$\text{Var}(X_i) = \sigma^2 \in (0, \infty)$$.

1. **Linear Growth of Total Variance under Independence:**
   For a general sum $$S_n = \sum_{i=1}^n X_i$$, the variance expands as:
   $$
   \text{Var}(S_n) = \sum_{i=1}^n \text{Var}(X_i) + 2 \sum_{1 \le i < j \le n} \text{Cov}(X_i, X_j)
   $$
   Because independence implies $$\text{Cov}(X_i, X_j) = 0$$ for all $$i \neq j$$, all cross-covariance terms vanish and the variance of the sum scales linearly with $$n$$:
   $$
   \text{Var}(S_n) = n\sigma^2 \implies \text{SD}(S_n) = \sqrt{n}\,\sigma
   $$

2. **$$1/\sqrt{n}$$ Decay of the Sample Mean Standard Error:**
   Dividing $$S_n$$ by $$n$$ to form the sample average $$\bar{X}_n = \frac{1}{n}S_n$$ scales the variance by $$1/n^2$$:
   $$
   \text{Var}(\bar{X}_n) = \frac{1}{n^2}\text{Var}(S_n) = \frac{n\sigma^2}{n^2} = \frac{\sigma^2}{n} \implies \text{SE}(\bar{X}_n) = \frac{\sigma}{\sqrt{n}}
   $$

3. **Critical Rescaling to Stabilize Asymptotic Fluctuations:**
   Consider rescaling the estimation error $$(\bar{X}_n - \mu)$$ by a power law $$n^{\gamma}$$:
   $$
   \text{Var}\left(n^{\gamma}(\bar{X}_n - \mu)\right) = n^{2\gamma} \cdot \frac{\sigma^2}{n} = \sigma^2 \, n^{2\gamma - 1}
   $$
   * If **$$\gamma = 0$$** (unscaled error $$\bar{X}_n - \mu$$), variance decays as $$\sigma^2 / n \to 0$$, collapsing the distribution to a degenerate Dirac point mass at $$0$$ (the LLN).
   * If **$$\gamma > 1/2$$** (e.g., $$\gamma = 1$$, total error $$S_n - n\mu$$), variance grows as $$\sigma^2 n^{2\gamma - 1} \to \infty$$, so the distribution spreads out without bound.
   * **Unique Non-Degenerate Exponent ($$\gamma = 1/2 \iff n^{\gamma} = \sqrt{n}$$):** Setting $$2\gamma - 1 = 0$$ gives $$\gamma = 1/2$$, stabilizing the variance at $$\sigma^2$$ for every $$n$$ so that $$\sqrt{n}(\bar{X}_n - \mu)$$ converges in distribution to a non-trivial Gaussian law $$\mathcal{N}(0, \sigma^2)$$.

4. **Impact of Dependence and Inference Precision:**
   * **Short-Range Dependence:** In stationary time series with summable autocovariances $$\sum_{k=-\infty}^{\infty} \gamma(k) = \sigma_{\text{LR}}^2 < \infty$$, variance still scales as $$O(n)$$, preserving the $$\sqrt{n}$$ rate with an inflated long-run variance $$\sigma_{\text{LR}}^2$$ (requiring HAC / Newey–West standard errors).
   * **Long-Range Dependence vs. Boundary Estimation:** If correlations decay slowly ($$\text{Cov}(X_t, X_{t+k}) \sim k^{-\alpha}$$ with $$0 < \alpha < 1$$), $$\text{Var}(S_n) \sim n^{2-\alpha}$$ and the normalizing factor becomes $$n^{\alpha/2}$$. Conversely, for extreme-order statistics such as $$\hat{\theta}_n = \max_{1 \le i \le n} X_i$$ on $$\text{Uniform}(0, \theta)$$, $$\text{Var}(\hat{\theta}_n) = O(n^{-2})$$, yielding a faster $$n$$-convergence rate rather than $$\sqrt{n}$$.
   * **Practical Statistical Inference:** Because confidence interval half-widths $$z_{1-\alpha/2}\frac{\sigma}{\sqrt{n}}$$ shrink at rate $$n^{-1/2}$$, **doubling measurement precision requires quadrupling the sample size ($$4n$$)**.

---

### 2. Conditional Probability, Bayes' Theorem & Statistical Inference

#### Conditional Probability & Derivation of Bayes' Theorem

In Kolmogorov probability theory, for two events $$A, B \in \mathcal{F}$$ with $$P(B) > 0$$, the **conditional probability** of $$A$$ given $$B$$ restricts the sample space to $$B$$ and renormalizes measure:

$$
P(A \mid B) = \frac{P(A \cap B)}{P(B)}
$$

By symmetry of intersection ($$P(A \cap B) = P(B \mid A)P(A)$$) and the **Law of Total Probability** over a measurable partition $$\{A_k\}$$, we obtain **Bayes' Theorem**:

$$
P(A_k \mid B) = \frac{P(B \mid A_k) \, P(A_k)}{\sum_{j} P(B \mid A_j) \, P(A_j)}
$$

In **parametric statistical inference**, the observable data $$\mathcal{D} = (x_1, \dots, x_n)$$ are generated by a probabilistic model indexed by an unknown parameter $$\theta \in \Theta$$. Treating $$\theta$$ itself as a random variable endowed with a probability distribution, Bayes' theorem inverts the generative direction $$p(\mathcal{D} \mid \theta)$$ into inferential uncertainty about $$\theta$$:

$$
\pi(\theta \mid \mathcal{D}) = \frac{p(\mathcal{D} \mid \theta) \, \pi(\theta)}{p(\mathcal{D})} = \frac{\mathcal{L}(\theta; \mathcal{D}) \, \pi(\theta)}{\int_{\Theta} \mathcal{L}(\tilde{\theta}; \mathcal{D}) \, \pi(\tilde{\theta}) \, d\tilde{\theta}} \propto \mathcal{L}(\theta; \mathcal{D}) \, \pi(\theta)
$$

#### The Three Core Components of Bayesian Inference

1. **Prior Distribution $$\pi(\theta)$$:**
   Represents epistemic beliefs, physical constraints, or historical baseline rates regarding $$\theta \in \Theta$$ *before* observing the current dataset $$\mathcal{D}$$. Priors may be **informative** (encoding validated domain knowledge), **weakly informative** (regularizing extreme parameter values), or **non-informative / reference** (e.g., Jeffreys prior $$\pi(\theta) \propto \sqrt{\det \mathcal{I}(\theta)}$$, invariant under reparameterization).
2. **Likelihood Function $$\mathcal{L}(\theta; \mathcal{D}) = p(\mathcal{D} \mid \theta)$$:**
   For independent observations $$\mathcal{D} = (x_1, \dots, x_n)$$, $$\mathcal{L}(\theta; \mathcal{D}) = \prod_{i=1}^n f(x_i \mid \theta)$$. Crucially, $$\mathcal{L}(\theta; \mathcal{D})$$ is viewed as a function of $$\theta$$ for *fixed* observed data $$\mathcal{D}$$; it is **not** a probability density in $$\theta$$ (its integral over $$\Theta$$ need not equal $$1$$), but serves as the conduit through which empirical evidence modifies prior weights.
3. **Posterior Distribution $$\pi(\theta \mid \mathcal{D})$$ (and Marginal Evidence $$p(\mathcal{D})$$):**
   The normalized product of prior and likelihood. It encapsulates *all* post-data uncertainty about $$\theta$$, enabling direct probability statements such as **Bayesian Credible Intervals** $$P(\theta \in [a, b] \mid \mathcal{D}) = 1 - \alpha$$ and **Posterior Predictive Distributions** $$p(x_{\text{new}} \mid \mathcal{D}) = \int_{\Theta} p(x_{\text{new}} \mid \theta)\pi(\theta \mid \mathcal{D})\,d\theta$$.

#### Why Sequential Bayesian Updating is Central to Inference

Suppose data arrive in two conditionally independent batches $$\mathcal{D}_1$$ and $$\mathcal{D}_2$$. Applying Bayes' rule sequentially—using the posterior from $$\mathcal{D}_1$$ as the prior for $$\mathcal{D}_2$$—yields:

$$
\pi(\theta \mid \mathcal{D}_1, \mathcal{D}_2) = \frac{p(\mathcal{D}_2 \mid \theta) \, \pi(\theta \mid \mathcal{D}_1)}{p(\mathcal{D}_2 \mid \mathcal{D}_1)} \propto p(\mathcal{D}_2 \mid \theta) \, p(\mathcal{D}_1 \mid \theta) \, \pi(\theta) = p(\mathcal{D}_1, \mathcal{D}_2 \mid \theta) \, \pi(\theta)
$$

* **Coherent Online Learning:** Sequential updating produces the **exact same posterior** as batch processing $$\mathcal{D}_1 \cup \mathcal{D}_2$$ simultaneously. This invariance makes Bayesian inference foundational for real-time telemetry filtering, adaptive clinical trials, and anomaly detection.
* **Asymptotic Bridge (Bernstein–von Mises Theorem):** Connecting Bayesian updating back to Section 1, as $$n \to \infty$$ under regularity conditions and a positive smooth prior $$\pi(\theta_0) > 0$$, the posterior concentrates around $$\theta_0$$ at rate $$\sqrt{n}$$ and converges in total variation to a Gaussian distribution centered at the Maximum Likelihood Estimator:
  $$
  \sqrt{n}\left(\theta - \hat{\theta}_{\text{MLE}}\right) \mid \mathcal{D} \xrightarrow{d} \mathcal{N}\left(0, \, \mathcal{I}(\theta_0)^{-1}\right)
  $$
  Thus, as sample size grows, empirical likelihood overwhelms the prior, reconciling Bayesian and frequentist asymptotics.

#### Concrete Analytical Examples

* **Example 1 — Conjugate Beta–Binomial Model (Conversion / Defect / Intrusion Rate $$\theta \in (0, 1)$$):**
  Suppose we observe $$k$$ successes in $$n$$ Bernoulli trials with likelihood $$\mathcal{L}(\theta; k, n) = \binom{n}{k}\theta^k (1-\theta)^{n-k}$$, paired with a conjugate prior $$\theta \sim \text{Beta}(\alpha_0, \beta_0)$$, $$\pi(\theta) \propto \theta^{\alpha_0 - 1}(1-\theta)^{\beta_0 - 1}$$. Multiplying prior and likelihood gives an exact Beta posterior:
  $$
  \theta \mid k, n \sim \text{Beta}(\alpha_n, \beta_n), \qquad \alpha_n = \alpha_0 + k, \quad \beta_n = \beta_0 + n - k
  $$
  The **posterior mean** is an explicit precision-weighted convex combination of the prior mean $$\mu_0 = \frac{\alpha_0}{\alpha_0 + \beta_0}$$ and the empirical MLE $$\hat{p} = \frac{k}{n}$$:
  $$
  \mathbb{E}[\theta \mid k, n] = \frac{\alpha_0 + k}{\alpha_0 + \beta_0 + n} = \underbrace{\left(\frac{\alpha_0 + \beta_0}{\alpha_0 + \beta_0 + n}\right)}_{\text{Prior Weight}} \mu_0 + \underbrace{\left(\frac{n}{\alpha_0 + \beta_0 + n}\right)}_{\text{Data Weight}} \hat{p}
  $$
  Here $$\kappa_0 = \alpha_0 + \beta_0$$ acts as an **effective prior sample size** (pseudo-counts).

* **Example 2 — Rare-Event Base-Rate Updating (Diagnostic / Security Alert Triage):**
  Suppose an automated intrusion detector has sensitivity $$P(\text{Alert} \mid \text{Attack}) = 0.99$$ and false-positive rate $$P(\text{Alert} \mid \text{Benign}) = 0.02$$, while true attacks occur with low prior prevalence $$P(\text{Attack}) = 0.005$$. Observing a single alert updates the attack probability to:
  $$
  P(\text{Attack} \mid \text{Alert}) = \frac{0.99 \times 0.005}{0.99 \times 0.005 + 0.02 \times 0.995} = \frac{0.00495}{0.02485} \approx 0.1992 \;(19.9\%)
  $$
  Ignoring the prior (the *base-rate fallacy*) would falsely equate a $$99\%$$ sensitivity with a $$99\%$$ posterior probability of attack.

* **Example 3 — Conjugate Normal–Normal Location Model (Gaussian Shrinkage):**
  For Gaussian data $$\bar{X}_n \mid \mu \sim \mathcal{N}(\mu, \sigma^2/n)$$ and prior $$\mu \sim \mathcal{N}(\mu_0, \tau_0^2)$$, posterior precision equals the sum of prior precision and sample precision ($$\tau_n^{-2} = \tau_0^{-2} + n/\sigma^2$$), shrinking noisy small-sample estimates toward $$\mu_0$$ (mathematically equivalent to $$L_2$$ Ridge regularization).

#### Applications, Advantages, Limitations & Influence on Conclusions

| Dimension | Key Characteristics & Impact on Statistical Conclusions |
| :--- | :--- |
| **Primary Applications** | Hierarchical / multilevel modeling, A/B testing with early stopping, medical diagnostics, spam/intrusion filtering, Kalman filtering, and small-sample forecasting. |
| **Advantages** | Exact finite-sample uncertainty quantification without relying on asymptotic CLT approximations; natural incorporation of historical knowledge; automatic regularization against overfitting in sparse data; seamless sequential updating. |
| **Limitations** | **Prior sensitivity** when sample size $$n$$ is small; **Cromwell's Rule** (assigning $$\pi(\theta) = 0$$ to a region makes posterior probability zero regardless of future data); analytical intractability of $$p(\mathcal{D})$$ in high-dimensional non-conjugate models, requiring Markov Chain Monte Carlo (MCMC) or Variational Inference. |
| **Influence on Conclusions** | In small samples ($$n \ll \kappa_0$$), an informative prior strongly pulls point estimates and narrows credible intervals toward $$\mu_0$$. As $$n \gg \kappa_0$$, the likelihood dominates and Bayesian credible intervals numerically align with frequentist confidence intervals. |

---

### 3. Interactive Probability & Bayesian Inference Lab

<div class="card py-4 my-4 z-depth-1" style="background-color: transparent; border: 1px solid rgba(150,150,150,0.3);" markdown="0">
    <h5 class="text-center mb-4 font-weight-bold">Interactive Asymptotic Convergence & Bayesian Updating Suite</h5>

    <!-- PART A: MODES OF CONVERGENCE & SQRT(N) SCALING -->
    <div class="px-4 mb-5">
        <h5 class="font-weight-bold text-center mb-3" style="color: #3498db;">1. Modes of Convergence (LLN, CLT & √n Scaling)</h5>

        <!-- Controls Row 1 -->
        <div class="d-flex flex-column flex-md-row align-items-center justify-content-center mb-3">
            <label for="convDistSelect" class="font-weight-bold mb-2 mb-md-0 mr-md-3" style="white-space: nowrap;">
                Parent Distribution Xᵢ:
            </label>
            <select id="convDistSelect" class="form-control" style="max-width: 460px; background-color: rgba(128,128,128,0.05); color: inherit;" onchange="runConvergenceLab()">
                <option value="bernoulli">Bernoulli(p = 0.30) — Discrete Skewed [μ = 0.30, σ = 0.458]</option>
                <option value="exponential">Exponential(λ = 1.0) — Continuous Right-Skewed [μ = 1.00, σ = 1.000]</option>
                <option value="uniform">Uniform(0, 1) — Bounded Symmetric [μ = 0.50, σ = 0.289]</option>
                <option value="pareto">Pareto(α = 2.5, xₘ = 1) — Heavy-Tailed Finite Variance [μ = 1.667, σ = 1.491]</option>
            </select>
        </div>

        <!-- Controls Row 2 -->
        <div class="row justify-content-center mb-3">
            <div class="col-md-4 mb-2 mb-md-0 text-center">
                <label class="font-weight-bold d-block text-center">Sample Size (n): <span id="convNVal" style="color: #3498db;">100</span></label>
                <input type="range" id="convNRange" class="custom-range" min="10" max="500" step="10" value="100" oninput="document.getElementById('convNVal').innerText = this.value; runConvergenceLab();">
            </div>
            <div class="col-md-4 mb-2 mb-md-0 text-center">
                <label class="font-weight-bold d-block text-center">Monte Carlo Trials (M): <span id="convMVal" style="color: #9b59b6;">400</span></label>
                <input type="range" id="convMRange" class="custom-range" min="100" max="1000" step="100" value="400" oninput="document.getElementById('convMVal').innerText = this.value; runConvergenceLab();">
            </div>
            <div class="col-md-4 text-center">
                <label class="font-weight-bold d-block text-center">Tolerance Threshold (ε): <span id="convEpsVal" style="color: #e67e22;">0.10</span></label>
                <input type="range" id="convEpsRange" class="custom-range" min="0.02" max="0.30" step="0.02" value="0.10" oninput="document.getElementById('convEpsVal').innerText = parseFloat(this.value).toFixed(2); runConvergenceLab();">
            </div>
        </div>

        <!-- Controls Row 3 -->
        <div class="d-flex justify-content-center mb-3">
            <button type="button" class="btn btn-sm btn-outline-primary px-4" onclick="runConvergenceLab()">Resimulate Sample Paths</button>
        </div>

        <!-- Metrics Table -->
        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th class="text-center">Theoretical Mean (μ)</th>
                        <th class="text-center">Standard Error (σ / √n)</th>
                        <th class="text-center">Empirical L² Error E[(X̄ₙ − μ)²]</th>
                        <th class="text-center">Theoretical L² (σ² / n)</th>
                        <th class="text-center">Tail Prob P(|X̄ₙ − μ| ≥ ε)</th>
                        <th class="text-center">CLT Scaled SD √n(X̄ₙ − μ)/σ</th>
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

        <!-- Charts Row -->
        <div class="row mt-4">
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center mb-3">LLN Sample Paths X̄ₖ → μ & ±1.96σ/√k Envelope</h6>
                <div style="position: relative; width: 100%; height: 270px;">
                    <canvas id="convPathsChart"></canvas>
                </div>
            </div>
            <div class="col-md-6 mb-4">
                <h6 class="font-weight-bold text-center mb-3">CLT Scaling: Unscaled (X̄ₙ − μ) vs. √n-Scaled Statistic</h6>
                <div style="position: relative; width: 100%; height: 270px;">
                    <canvas id="convCltChart"></canvas>
                </div>
            </div>
        </div>
    </div>

    <hr class="w-75 mx-auto mb-5" style="border-color: rgba(150,150,150,0.25);">

    <!-- PART B: BAYESIAN SEQUENTIAL UPDATING (BETA-BINOMIAL) -->
    <div class="px-4 mb-2">
        <h5 class="font-weight-bold text-center mb-3" style="color: #9b59b6;">2. Bayesian Sequential Updating Lab (Beta–Binomial Model)</h5>

        <!-- Preset Row -->
        <div class="d-flex flex-column flex-md-row align-items-center justify-content-center mb-3">
            <label for="bayesPriorPreset" class="font-weight-bold mb-2 mb-md-0 mr-md-3" style="white-space: nowrap;">
                Prior Belief Regime π(θ):
            </label>
            <select id="bayesPriorPreset" class="form-control" style="max-width: 460px; background-color: rgba(128,128,128,0.05); color: inherit;" onchange="onBayesPresetChange()">
                <option value="uniform">Non-Informative Uniform Prior: Beta(α₀ = 1, β₀ = 1)</option>
                <option value="jeffreys">Jeffreys Reference Prior: Beta(α₀ = 0.5, β₀ = 0.5)</option>
                <option value="skeptical">Skeptical Low-Rate Prior: Beta(α₀ = 2, β₀ = 18) [Mean = 0.10]</option>
                <option value="informative">Strong Informative Prior: Beta(α₀ = 15, β₀ = 15) [Mean = 0.50]</option>
            </select>
        </div>

        <!-- Sliders Row -->
        <div class="row justify-content-center mb-3">
            <div class="col-md-3 mb-2 mb-md-0 text-center">
                <label class="font-weight-bold d-block text-center">Prior α₀: <span id="bayesAlphaVal" style="color: #3498db;">1.0</span></label>
                <input type="range" id="bayesAlphaRange" class="custom-range" min="0.5" max="30" step="0.5" value="1" oninput="document.getElementById('bayesAlphaVal').innerText = parseFloat(this.value).toFixed(1); runBayesianLab();">
            </div>
            <div class="col-md-3 mb-2 mb-md-0 text-center">
                <label class="font-weight-bold d-block text-center">Prior β₀: <span id="bayesBetaVal" style="color: #3498db;">1.0</span></label>
                <input type="range" id="bayesBetaRange" class="custom-range" min="0.5" max="30" step="0.5" value="1" oninput="document.getElementById('bayesBetaVal').innerText = parseFloat(this.value).toFixed(1); runBayesianLab();">
            </div>
            <div class="col-md-3 mb-2 mb-md-0 text-center">
                <label class="font-weight-bold d-block text-center">Trials (n): <span id="bayesNVal" style="color: #2ecc71;">25</span></label>
                <input type="range" id="bayesNRange" class="custom-range" min="1" max="200" step="1" value="25" oninput="syncSuccessSlider(); runBayesianLab();">
            </div>
            <div class="col-md-3 text-center">
                <label class="font-weight-bold d-block text-center">Successes (k): <span id="bayesKVal" style="color: #e74c3c;">18</span></label>
                <input type="range" id="bayesKRange" class="custom-range" min="0" max="25" step="1" value="18" oninput="document.getElementById('bayesKVal').innerText = this.value; runBayesianLab();">
            </div>
        </div>

        <!-- Summary Posterior Table -->
        <div class="table-responsive my-3">
            <table class="table table-sm table-bordered text-center mb-0" style="color: inherit; border-color: rgba(150,150,150,0.3);">
                <thead style="background-color: rgba(128,128,128,0.1);">
                    <tr>
                        <th class="text-center">Prior Mean E[θ]</th>
                        <th class="text-center">Likelihood MLE (k / n)</th>
                        <th class="text-center">Posterior Beta(αₙ, βₙ)</th>
                        <th class="text-center">Posterior Mean E[θ | D]</th>
                        <th class="text-center">Posterior Mode (MAP)</th>
                        <th class="text-center">95% Credible Interval</th>
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

        <!-- Bayesian Curves Chart -->
        <div class="row justify-content-center mt-4">
            <div class="col-lg-10">
                <h6 class="font-weight-bold text-center mb-3">Bayesian Updating: Prior π(θ), Scaled Likelihood L(θ; k, n) & Posterior π(θ | k, n)</h6>
                <div style="position: relative; width: 100%; height: 290px;">
                    <canvas id="bayesUpdateChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Dynamic Insight Box -->
        <div class="mt-3">
            <div id="bayesInsightBox" class="p-3 rounded text-center" style="background-color: rgba(128,128,128,0.07); border-top: 3px solid #9b59b6;">
                Initializing Bayesian updating engine...
            </div>
        </div>
    </div>
</div>

<!-- Interactive Scripts -->
{% raw %}
<script src="[https://cdn.jsdelivr.net/npm/chart.js](https://cdn.jsdelivr.net/npm/chart.js)"></script>
<script>
let convPathsChartInstance = null;
let convCltChartInstance = null;
let bayesUpdateChartInstance = null;

// --- PART 1: MODES OF CONVERGENCE & CLT SIMULATION ---

function getParentDistribution(type) {
    switch (type) {
        case "bernoulli": {
            const p = 0.30;
            return {
                mean: p,
                sd: Math.sqrt(p * (1 - p)),
                sample: () => (Math.random() < p ? 1.0 : 0.0)
            };
        }
        case "exponential": {
            const rate = 1.0;
            return {
                mean: 1.0 / rate,
                sd: 1.0 / rate,
                sample: () => -Math.log(1.0 - Math.random()) / rate
            };
        }
        case "uniform": {
            return {
                mean: 0.5,
                sd: Math.sqrt(1.0 / 12.0),
                sample: () => Math.random()
            };
        }
        case "pareto": {
            // Pareto(alpha=2.5, xm=1): mean = alpha/(alpha-1) = 5/3, var = alpha / ((alpha-1)^2 * (alpha-2))
            const alpha = 2.5;
            const xm = 1.0;
            const mean = (alpha * xm) / (alpha - 1.0);
            const variance = (xm * xm * alpha) / (Math.pow(alpha - 1.0, 2) * (alpha - 2.0));
            return {
                mean: mean,
                sd: Math.sqrt(variance),
                sample: () => xm / Math.pow(1.0 - Math.random(), 1.0 / alpha)
            };
        }
        default:
            return { mean: 0.5, sd: Math.sqrt(1 / 12), sample: () => Math.random() };
    }
}

function runConvergenceLab() {
    const distEl = document.getElementById("convDistSelect");
    if (!distEl) return;

    const distType = distEl.value;
    const n = parseInt(document.getElementById("convNRange").value, 10);
    const M = parseInt(document.getElementById("convMRange").value, 10);
    const eps = parseFloat(document.getElementById("convEpsRange").value);

    const dist = getParentDistribution(distType);
    const mu = dist.mean;
    const sigma = dist.sd;
    const theoSE = sigma / Math.sqrt(n);
    const theoL2 = (sigma * sigma) / n;

    // Generate M Monte Carlo trials of length n
    const numPathsToPlot = 8;
    const samplePaths = [];
    const sampleMeans = new Array(M);
    const unscaledErrors = new Array(M);
    const cltScaled = new Array(M);

    let sqErrorSum = 0.0;
    let tailCount = 0;

    for (let m = 0; m < M; m++) {
        let runningSum = 0.0;
        let path = m < numPathsToPlot ? new Array(n) : null;

        for (let i = 0; i < n; i++) {
            runningSum += dist.sample();
            if (path) {
                path[i] = runningSum / (i + 1);
            }
        }

        if (path) samplePaths.push(path);

        let xBar = runningSum / n;
        let err = xBar - mu;
        sampleMeans[m] = xBar;
        unscaledErrors[m] = err;
        cltScaled[m] = (Math.sqrt(n) * err) / sigma;

        sqErrorSum += err * err;
        if (Math.abs(err) >= eps) tailCount++;
    }

    let empL2 = sqErrorSum / M;
    let tailProb = tailCount / M;
    let cltMean = cltScaled.reduce((a, b) => a + b, 0) / M;
    let cltVar = cltScaled.reduce((a, b) => a + Math.pow(b - cltMean, 2), 0) / M;

    // Update Summary Table
    document.getElementById("convTrueMean").innerText = mu.toFixed(4);
    document.getElementById("convSE").innerText = theoSE.toFixed(4);
    document.getElementById("convEmpL2").innerText = empL2.toExponential(3);
    document.getElementById("convTheoL2").innerText = theoL2.toExponential(3);
    document.getElementById("convTailProb").innerText = tailProb.toFixed(4);
    document.getElementById("convCltSd").innerText = Math.sqrt(cltVar).toFixed(3);

    renderConvergenceCharts(n, mu, sigma, samplePaths, unscaledErrors, cltScaled);
}

function renderConvergenceCharts(n, mu, sigma, samplePaths, unscaledErrors, cltScaled) {
    if (typeof Chart === "undefined") return;

    // Chart 1: Sample Paths converging to mu inside +/- 1.96 * sigma / sqrt(k)
    const step = Math.max(1, Math.floor(n / 60));
    const kLabels = [];
    const muLine = [];
    const upperEnv = [];
    const lowerEnv = [];

    for (let k = 1; k <= n; k += step) {
        kLabels.push(k);
        muLine.push(mu);
        let band = 1.96 * sigma / Math.sqrt(k);
        upperEnv.push(mu + band);
        lowerEnv.push(mu - band);
    }

    const pathColors = ["#3498db", "#9b59b6", "#2ecc71", "#e67e22", "#1abc9c", "#f1c40f", "#e84393", "#00cec9"];
    const datasets1 = samplePaths.map((path, idx) => {
        const sub = [];
        for (let k = 1; k <= n; k += step) {
            sub.push(path[k - 1]);
        }
        return {
            label: `Path ${idx + 1}`,
            data: sub,
            borderColor: pathColors[idx % pathColors.length],
            borderWidth: 1.4,
            pointRadius: 0,
            fill: false
        };
    });

    datasets1.push(
        {
            label: "True Mean μ",
            data: muLine,
            borderColor: "#e74c3c",
            borderWidth: 2.2,
            borderDash: [5, 4],
            pointRadius: 0,
            fill: false
        },
        {
            label: "+1.96 σ/√k",
            data: upperEnv,
            borderColor: "rgba(150,150,150,0.55)",
            borderWidth: 1.2,
            borderDash: [3, 3],
            pointRadius: 0,
            fill: false
        },
        {
            label: "-1.96 σ/√k",
            data: lowerEnv,
            borderColor: "rgba(150,150,150,0.55)",
            borderWidth: 1.2,
            borderDash: [3, 3],
            pointRadius: 0,
            fill: false
        }
    );

    const ctxPaths = document.getElementById("convPathsChart").getContext("2d");
    if (convPathsChartInstance) convPathsChartInstance.destroy();

    convPathsChartInstance = new Chart(ctxPaths, {
        type: "line",
        data: { labels: kLabels, datasets: datasets1 },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { display: false } },
            scales: {
                x: { title: { display: true, text: "Sample Size (k)" } },
                y: { title: { display: true, text: "Sample Mean X̄ₖ" } }
            }
        }
    });

    // Chart 2: Density Histogram on [-3.5, 3.5] comparing Unscaled (Xbar - mu) vs CLT-scaled sqrt(n)*(Xbar - mu)/sigma vs N(0,1)
    const numBins = 28;
    const minX = -3.5;
    const maxX = 3.5;
    const binW = (maxX - minX) / numBins;

    const binCenters = [];
    const cltHist = new Array(numBins).fill(0);
    const unscaledHist = new Array(numBins).fill(0);
    const normalCurve = [];

    const M = cltScaled.length;
    for (let m = 0; m < M; m++) {
        let zVal = cltScaled[m];
        let b1 = Math.floor((zVal - minX) / binW);
        if (b1 >= 0 && b1 < numBins) cltHist[b1]++;

        let uVal = unscaledErrors[m];
        let b2 = Math.floor((uVal - minX) / binW);
        if (b2 >= 0 && b2 < numBins) unscaledHist[b2]++;
    }

    for (let b = 0; b < numBins; b++) {
        let center = minX + (b + 0.5) * binW;
        binCenters.push(center.toFixed(2));
        cltHist[b] = cltHist[b] / (M * binW);
        unscaledHist[b] = unscaledHist[b] / (M * binW);
        normalCurve.push((1.0 / Math.sqrt(2.0 * Math.PI)) * Math.exp(-0.5 * center * center));
    }

    const ctxClt = document.getElementById("convCltChart").getContext("2d");
    if (convCltChartInstance) convCltChartInstance.destroy();

    convCltChartInstance = new Chart(ctxClt, {
        data: {
            labels: binCenters,
            datasets: [
                {
                    type: "line",
                    label: "Standard Normal N(0, 1)",
                    data: normalCurve,
                    borderColor: "#e74c3c",
                    borderWidth: 2.2,
                    pointRadius: 0,
                    fill: false,
                    tension: 0.3
                },
                {
                    type: "bar",
                    label: "CLT Scaled √n(X̄ₙ − μ)/σ",
                    data: cltHist,
                    backgroundColor: "rgba(52, 152, 219, 0.45)",
                    borderColor: "#3498db",
                    borderWidth: 1
                },
                {
                    type: "bar",
                    label: "Unscaled Error (X̄ₙ − μ)",
                    data: unscaledHist,
                    backgroundColor: "rgba(155, 89, 182, 0.35)",
                    borderColor: "#9b59b6",
                    borderWidth: 1
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: "Value" } },
                y: { title: { display: true, text: "Probability Density" }, beginAtZero: true }
            }
        }
    });
}

// --- PART 2: BAYESIAN SEQUENTIAL UPDATING (BETA-BINOMIAL) ---

function logGammaBayes(z) {
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

function betaPdf(x, a, b) {
    if (x <= 0.0 || x >= 1.0) return 0.0;
    let logB = logGammaBayes(a) + logGammaBayes(b) - logGammaBayes(a + b);
    let logVal = (a - 1.0) * Math.log(x) + (b - 1.0) * Math.log(1.0 - x) - logB;
    return Math.exp(logVal);
}

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

function syncSuccessSlider() {
    const n = parseInt(document.getElementById("bayesNRange").value, 10);
    document.getElementById("bayesNVal").innerText = n;
    const kSlider = document.getElementById("bayesKRange");
    const oldMax = parseInt(kSlider.max, 10) || 25;
    const oldK = parseInt(kSlider.value, 10);
    const prop = oldK / oldMax;
    kSlider.max = n;
    const newK = Math.min(n, Math.round(prop * n));
    kSlider.value = newK;
    document.getElementById("bayesKVal").innerText = newK;
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
    const postMode = (aPost > 1.0 && bPost > 1.0)
        ? ((aPost - 1.0) / (aPost + bPost - 2.0)).toFixed(4)
        : (aPost <= 1.0 && bPost > 1.0 ? "0.0000 (boundary)" : (bPost <= 1.0 && aPost > 1.0 ? "1.0000 (boundary)" : "Bimodal (0 & 1)"));

    // Grid evaluation on (0, 1) for curves and numerical 95% credible interval
    const steps = 200;
    const dx = 1.0 / steps;
    const thetaLabels = [];
    const priorCurve = [];
    const likCurve = [];
    const postCurve = [];

    // Compute normalized likelihood Beta(k+1, n-k+1) for clear visual scale comparison
    let cumProb = 0.0;
    let ciLow = 0.0;
    let ciHigh = 1.0;
    let foundLow = false;

    for (let i = 1; i < steps; i++) {
        let th = i * dx;
        thetaLabels.push(th.toFixed(2));
        let pVal = betaPdf(th, a0, b0);
        let lVal = betaPdf(th, k + 1.0, (n - k) + 1.0);
        let postVal = betaPdf(th, aPost, bPost);

        priorCurve.push(isFinite(pVal) ? pVal : null);
        likCurve.push(isFinite(lVal) ? lVal : null);
        postCurve.push(isFinite(postVal) ? postVal : null);

        if (isFinite(postVal)) {
            cumProb += postVal * dx;
            if (!foundLow && cumProb >= 0.025) {
                ciLow = th;
                foundLow = true;
            }
            if (cumProb <= 0.975) {
                ciHigh = th;
            }
        }
    }

    document.getElementById("bayesPriorMean").innerText = priorMean.toFixed(4);
    document.getElementById("bayesMle").innerText = mle.toFixed(4);
    document.getElementById("bayesPostParams").innerText = `Beta(${aPost.toFixed(1)}, ${bPost.toFixed(1)})`;
    document.getElementById("bayesPostMean").innerText = postMean.toFixed(4);
    document.getElementById("bayesPostMap").innerText = postMode;
    document.getElementById("bayesPostCI").innerText = `[${ciLow.toFixed(3)}, ${ciHigh.toFixed(3)}]`;

    const priorWeight = (a0 + b0) / (a0 + b0 + n);
    const dataWeight = n / (a0 + b0 + n);
    const insightBox = document.getElementById("bayesInsightBox");
    if (insightBox) {
        insightBox.innerHTML = `<strong>Posterior Compromise Decomposition:</strong> The posterior mean <strong>E[θ | D] = ${postMean.toFixed(4)}</strong> equals <strong>${(priorWeight * 100).toFixed(1)}%</strong> of the prior mean (${priorMean.toFixed(4)}) plus <strong>${(dataWeight * 100).toFixed(1)}%</strong> of the empirical likelihood MLE (${mle.toFixed(4)}). As sample size <em>n</em> increases, data weight approaches 100% and the posterior sharpens around the MLE.`;
    }

    renderBayesianChart(thetaLabels, priorCurve, likCurve, postCurve);
}

function renderBayesianChart(labels, priorData, likData, postData) {
    if (typeof Chart === "undefined") return;

    const ctxBayes = document.getElementById("bayesUpdateChart").getContext("2d");
    if (bayesUpdateChartInstance) bayesUpdateChartInstance.destroy();

    bayesUpdateChartInstance = new Chart(ctxBayes, {
        type: "line",
        data: {
            labels: labels,
            datasets: [
                {
                    label: "Prior Density π(θ)",
                    data: priorData,
                    borderColor: "#3498db",
                    borderWidth: 2,
                    borderDash: [5, 4],
                    pointRadius: 0,
                    fill: false,
                    tension: 0.25
                },
                {
                    label: "Normalized Likelihood L(θ; k, n)",
                    data: likData,
                    borderColor: "#2ecc71",
                    borderWidth: 2,
                    pointRadius: 0,
                    fill: false,
                    tension: 0.25
                },
                {
                    label: "Posterior Density π(θ | k, n)",
                    data: postData,
                    borderColor: "#9b59b6",
                    backgroundColor: "rgba(155, 89, 182, 0.22)",
                    borderWidth: 2.5,
                    pointRadius: 0,
                    fill: true,
                    tension: 0.25
                }
            ]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: { title: { display: true, text: "Parameter Value (θ)" } },
                y: { title: { display: true, text: "Probability Density" }, beginAtZero: true }
            }
        }
    });
}

function tryInitHomework7() {
    if (typeof Chart === "undefined" || !document.getElementById("convPathsChart") || !document.getElementById("bayesUpdateChart")) {
        setTimeout(tryInitHomework7, 50);
        return;
    }
    runConvergenceLab();
    runBayesianLab();
}

if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", tryInitHomework7);
} else {
    tryInitHomework7();
}
</script>
{% endraw %}
