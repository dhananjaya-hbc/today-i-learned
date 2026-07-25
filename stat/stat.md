# 📊 Mathematical Statistics – Learning Journey

> Personal learning notes and exam preparation for Mathematical Statistics.

---

# 📚 Topics Covered

- Probability Distributions (PMF & PDF)
- Expected Value
- Method of Moments (MOM)
- Maximum Likelihood Estimation (MLE)
- Sufficiency & Factorization Theorem
- Confidence Intervals
- Hypothesis Testing
- Z-Test
- One-Sample t-Test
- Paired t-Test
- Chi-Square Distribution
- Sample Variance
- Variance of Sample Variance

---

# 1. PMF vs PDF

| PMF | PDF |
|------|-----|
| Used for discrete random variables | Used for continuous random variables |
| ΣP(X=x)=1 | ∫f(x)dx=1 |

Example PMF

```text
P(X=x)=p(1-p)^x
```

Example PDF

```text
f(x)=(θ+1)x^θ, 0<x<1
```

---

# 2. Expected Value

Discrete

```text
E(X)=ΣxP(X=x)
```

Continuous

```text
E(X)=∫xf(x)dx
```

---

# 3. Method of Moments (MOM)

Steps

1. Find the theoretical moment.
2. Find the sample moment.
3. Equate them.
4. Solve for the unknown parameter.

Example

```text
E(X)=(θ+1)/(θ+2)

x̄=E(X)

θ̂=(2x̄−1)/(1−x̄)
```

---

# 4. Maximum Likelihood Estimation (MLE)

Steps

1. Write the likelihood function.
2. Take the log-likelihood.
3. Differentiate.
4. Set derivative equal to zero.
5. Verify second derivative is negative.

Example (Geometric)

```text
L(p)=pⁿ(1−p)^(Σx)

p̂=n/(n+Σx)
```

---

# 5. Sufficiency

Factorization Theorem

```text
L(θ)=g(T(x),θ)h(x)
```

Then T(X) is sufficient.

Example

```text
T(X)=ΣXi
```

---

# 6. Confidence Intervals

Known variance

```text
x̄ ± zα/2 (σ/√n)
```

Unknown variance

```text
x̄ ± tα/2,n−1 (s/√n)
```

---

# 7. Hypothesis Testing

1. State H₀ and H₁
2. Choose α
3. Calculate test statistic
4. Find critical value or p-value
5. Make decision

Decision Rule

```text
p-value < α  → Reject H₀
p-value ≥ α  → Do Not Reject H₀
```

---

# 8. Z-Test

Use when population variance is known.

```text
Z=(x̄−μ₀)/(σ/√n)
```

---

# 9. One-Sample t-Test

Use when population variance is unknown.

```text
t=(x̄−μ₀)/(s/√n)

df=n−1
```

---

# 10. Paired t-Test

Difference

```text
D=Before−After
```

Hypotheses

```text
H₀: μD=0
H₁: μD>0
```

Statistic

```text
t=(d̄−μ₀)/(sD/√n)
```

---

# 11. Chi-Square Distribution

Important Result

```text
((n−1)S²)/σ² ~ χ²(n−1)
```

Properties

```text
Mean = k

Variance = 2k
```

---

# 12. Variance of Sample Variance

```text
Var(S²)=2σ⁴/(n−1)
```

---

# 13. Important Formulas

| Concept | Formula |
|---------|---------|
| Sample Mean | x̄=(ΣXi)/n |
| Sample Variance | S²=Σ(Xi−x̄)²/(n−1) |
| Standard Error (Known σ) | σ/√n |
| Standard Error (Unknown σ) | s/√n |
| Z Statistic | (x̄−μ₀)/(σ/√n) |
| t Statistic | (x̄−μ₀)/(s/√n) |

---

# 🎯 What I Learned

- Difference between PMF and PDF
- Expected value calculations
- Method of Moments
- Maximum Likelihood Estimation
- Sufficiency
- Confidence intervals
- Z-tests
- t-tests
- Paired t-tests
- p-values
- Chi-square distribution
- Variance of sample variance
- Solving university exam questions step by step

---

# 🛠 Tools

- Markdown
- Git & GitHub
- ChatGPT

---

# 👨‍🎓 Author

**Shehan Bandara**

University of Moratuwa  
Department of Information Technology

---

⭐ This repository documents my Mathematical Statistics learning journey through theory, derivations, formulas, and exam-style solutions.
