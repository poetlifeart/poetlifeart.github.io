---
title: "Offline RL-1"
date: 2025-07-12
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-1/  
tags:
- reinforcement learning
- policy gradient
- mathematics
layout: post

---



<!-- Load MathJax so LaTeX renders in GitHub Pages without touching layouts -->
<script>
  window.MathJax = {
    tex: {
      inlineMath: [['\\(','\\)'], ['\\[','\\]']]
    }
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>


In this post, we walk through the step-by-step derivations of some of the math in the 
paper [ Offline Reinforcement Learning: Tutorial, Review, and Perspectives on Open Problems](https://arxiv.org/abs/2005.01643), at times provide a deeper discussion and correct minor mistakes and typos in that paper. The best way is to read the paper and use this blog to clarify various topics. Our goal is not to replicate the paper but to supplement it. 

## 1. Objective: Maximizing Expected Return

Let's start with (2) on page 4; and provide a detailed proof. 
We aim to maximize the expected return under a parameterized policy \\(\pi_\theta\\):

$$
\begin{align}
\nabla_\theta J(\pi_\theta)
&= \mathbb{E}_{\tau\sim\pi_\theta}\Bigl[\sum_{t=0}^H \gamma^t\nabla_\theta\log\pi_\theta(a_t\!\mid\!s_t)\,R_{t:H}(\tau)\Bigr]\\
&= \sum_{t=0}^H \mathbb{E}_{\tau}\bigl[\gamma^t\nabla_\theta\log\pi_\theta(a_t\!\mid\!s_t)\,R_{t:H}(\tau)\bigr].
\end{align}
$$


$$
J(\theta) = \mathbb{E}_{\tau \sim \pi_\theta}[R(\tau)], \quad R(\tau) = \sum_{t=0}^H \gamma^t r(s_t,a_t).
$$

Here, \\(\tau=(s_0,a_0,\dots,s_H,a_H)\\) is a trajectory, and \\(\gamma\in[0,1]\\) is the discount factor.

## 2. Applying the Log-Derivative Trick

$$
\nabla_{\theta}J(\theta)
= \nabla_{\theta}\,\mathbb{E}_{\tau\sim\pi_{\theta}}\bigl[R(\tau)\bigr]
= \mathbb{E}_{\tau\sim\pi_{\theta}}\!\bigl[\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)\,R(\tau)\bigr].
$$

proof of the trick:

\begin{equation}
\nabla_\theta p(\tau;\theta)
= p(\tau;\theta)\,\nabla_\theta \log p(\tau;\theta)
\end{equation}

$$
\begin{align}
\nabla_\theta \mathbb{E}_{\tau\sim\pi_\theta}[R(\tau)]
&= \nabla_\theta \int p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int \nabla_\theta p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \int p_{\pi_\theta}(\tau)\,\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\,\mathrm{d}\tau \\
&= \mathbb{E}_{\tau\sim\pi_\theta}\bigl[\nabla_\theta \log p_{\pi_\theta}(\tau)\,R(\tau)\bigr] \\
&= \nabla_\theta J(\theta)\,.
\end{align}
$$




Decompose  \\(\log p_{\pi_{\theta}}(\tau)\\)

$$
p(\tau)
= \rho_0(s_0)\,\prod_{t=0}^{H}\pi_{\theta}(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t),
$$

so

$$
\nabla_{\theta}\log p_{\pi_{\theta}}(\tau)
= \sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t).
$$

Plug in:

$$
\nabla_{\theta}J(\theta)
= \mathbb{E}_{\tau}\Bigl[\Bigl(\sum_{t=0}^{H}\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\Bigr)\,R(\tau)\Bigr]
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,R(\tau)\bigr].
$$


## 3. Introducing the Reward-to-Go (Causality)

Only future rewards depend on \\(a_t\\). Dropping past rewards:
(mathematically if you write out the integral terms that do not depend on the integrator 
factor out and you are left with the gradient of integral of a probability which is the constant 1 and hence zero)

$$
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\underbrace{\sum_{t'=t}^{H}\gamma^{t'}r(s_{t'},a_{t'})}_{\text{reward‐to‐go}}\Bigr].
$$

Factor out \\(\gamma^t\\):

$$

= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'})\Bigr].
$$

Add a Baseline (As to what a baseline is and why we subtract see pp 329 of Sutton, but here we give the proof)}

Subtract a baseline \\(b(s_t)\\):
$$
\nabla_{\theta}J(\theta)
= \sum_{t=0}^{H}\mathbb{E}_{\tau}\Bigl[\gamma^t\,\nabla_{\theta}\log\pi_{\theta}(a_t\mid s_t)\,
\Bigl(\sum_{t'=t}^{H}\gamma^{\,t'-t}r(s_{t'},a_{t'}) - b(s_t)\Bigr)\Bigr].
$$


proof that you can subtract the base so long as it is a function of state alone:


$$
\begin{align*}
\nabla_{\theta} J(\pi_{\theta}) 
&= \mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
\sum_{t=0}^{H}\gamma^{t}\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\left(\sum_{t'=t}^{H}\gamma^{t'-t}r(s_{t'},a_{t'}) - b(s_{t})\right)
\right]
\end{align*}
$$

We explicitly verify that the baseline is unbiased:

$$
\begin{align*}
&\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
\sum_{t=0}^{H}\gamma^{t}\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t}) b(s_{t})
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\mathbb{E}_{a_{t}\sim\pi_{\theta}(a_{t}|s_{t})}\left[\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\middle|s_{t}\right]
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\int_{a_t}\pi_{\theta}(a_{t}|s_{t})\nabla_{\theta}\log\pi_{\theta}(a_{t}|s_{t})\,da_{t}
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t})\int_{a_t}\nabla_{\theta}\pi_{\theta}(a_{t}|s_{t})\,da_{t}
\right] \\
&\quad= \sum_{t=0}^{H}\gamma^{t}\mathbb{E}_{\tau\sim p_{\pi_{\theta}}(\tau)}\left[
b(s_{t}) \cdot 0
\right] \\
&\quad= 0
\end{align*}
$$



Erratum note: finite‐ to infinite‐horizon carry–over typo

In the finite‐horizon derivation we write
$$
\nabla_{\theta}
= \sum_{t=0}^{H}
\mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}}
\bigl[\gamma^{t}\,\nabla_{\theta}\log\pi_{\theta}(a_{t}\mid s_{t})\,\hat{A}(s_{t},a_{t})\bigr].
$$

Here both the state‐distribution (\\(d_{t}^{\pi}\\) and the discount weight \\(\gamma^{t}\\) are explicitly indexed by \\(t\\).

When passing to the infinite‐horizon form, those two pieces are folded into a single “discounted occupancy” measure

$$
d^{\pi}(s)\;=\;(1-\gamma)\sum_{t=0}^{\infty}\gamma^{t}\,d_{t}^{\pi}(s),
$$

We can now absorb the discount factor and the sum into the measure \\(d_{t}^{\pi}(s)\\) (tutorial characterizes this as "dropping" the discount factor but this is not really an accurate description)  so that the gradient can be written as

$$
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi},\,a\sim\pi_{\theta}}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr].
$$

The stray subscript “\\(_t\\)” on the state distribution in the infinite‐horizon equation was simply a leftover from the finite‐horizon version. The correct infinite‐horizon line should read


$$
\nabla_{\theta}J
= \frac{1}{1-\gamma}\,
\mathbb{E}_{s\sim d^{\pi}(s),\,a\sim\pi_{\theta}(a\mid s)}
\bigl[\nabla_{\theta}\log\pi_{\theta}(a\mid s)\,\hat{A}(s,a)\bigr],
$$

with no time index on \\(d^{\pi}\\).

## 4. Proof of measure absorption 

If we represent measures as densities, this is easy to show:

$$
\frac{1}{1-\gamma}\mathbb{E}_{s\sim d^\pi(s),\,a\sim\pi_{\theta}(a|s)}[f(s,a)]
= \frac{1}{1-\gamma}\int_{s}\int_{a} d^\pi(s)\pi_{\theta}(a|s)f(s,a)\,da\,ds
$$

Plug in the definition of \\( d^\pi(s) \\):

$$
= \frac{1}{1-\gamma}\int_{s}\int_{a}\left[\\(1-\gamma\\)\sum_{t=0}^{\infty}\gamma^{t}d_{t}^{\pi}(s)\right]\pi_{\theta}(a|s)f(s,a)\,da\,ds
$$

Simplify the factor \\((1-\gamma)\\):

$$
= \sum_{t=0}^{\infty}\gamma^{t}\int_{s}\int_{a}d_{t}^{\pi}(s)\pi_{\theta}(a|s)f(s,a)\,da\,ds
$$

Recognize that:

$$
\int_{s}\int_{a} d_{t}^{\pi}(s)\pi_{\theta}(a|s)f(s,a)\,da\,ds
= \mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}(a_{t}|s_{t})}[f(s_{t},a_{t})]
$$

The variables in the integrant at his point are just labels and we can replace them
with any symbol we wish, in this case \\(s_t\\) for \\(s_t\\), we can then replace 
\\(a_t\\) for \\(a\\) because the control conditioned on \\(s_t\\) is by definition \\(a_t\\). Hence, indeed:

$$
= \sum_{t=0}^{\infty}\gamma^{t}\mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}(a_{t}|s_{t})}[f(s_{t},a_{t})],
$$

which is exactly our starting point.

Thus, we've explicitly verified that:

$$
\sum_{t=0}^{\infty}\gamma^{t}\mathbb{E}_{s_{t}\sim d_{t}^{\pi},\,a_{t}\sim\pi_{\theta}(a_{t}|s_{t})}[f(s_{t},a_{t})]
= \frac{1}{1-\gamma}\mathbb{E}_{s\sim d^\pi(s),\,a\sim\pi_{\theta}(a|s)}[f(s,a)],
$$

proving clearly and explicitly why you can remove the explicit \\(t\\)-index. We are using \\(H\\) and \\(\infty\\) interchangeably. This can be justified, as Thomas points out, by assuming that there is only one admissible action in terminal states, and it causes a transition to an absorbing state with zero reward, which we call a postterminal absorbing state, unifying the treatment of infinite horizon and episodic cases (where exit time H is random and
varies from episode to episode). 

Sutton treats the episodic case without a discount factor directly as well as the continuing average case. 
The deterministic finite time is not covered in the paper or by Sutton but in that case the average measure over 
the finite time can be used to replicate these results.   


While a simple time-dependent baseline \\(b_t = \frac{1}{N} \sum_{i=1}^N R_{i,t}\\) is often used to reduce variance in Monte Carlo policy gradient estimators, it does not represent a true value function \\(V(s_t) = \mathbb{E}[R_t \mid s_t]\\). This is because \\(b_t\\) marginalizes over all states \\(s_t\\) encountered at time \\(t\\) rather than conditioning on a specific state. Thus, it captures only the average return under the state visitation distribution \\(d_t(s)\\), not the expected return from a particular state \\(s_t = s\\). Although useful for variance reduction, this approach lacks the precision and generalization capabilities of a learned, state-dependent baseline. 

![Figure 1: Algorithm 1 On-policy policy gradient with Monte Carlo estimator](/images/Algorithm1.png)


Here fit \\( b(s_t)\\) to \\({Ri, t}\\) could mean some parameterized function of \\(s_t\\) such as a linear function. With such a fit we are very close to Actor-Critique model. The only difference is that here the state dependent baseline is obtained via monte carlo method while in proper Actor-Critique model, as we shall see, there is bootstrapping. 

## 5. Approximate dynamic programming 

The discussion in this section is pretty straightforward; we only remind ourselves what makes a policy "off-policy".  RL is off-policy  when the behavior policy, used to generate the training data, and the target policy,  the policy we are optimizing using that data, are not the same. So in Q-learning algorithm below:  

![Figure 2: Q-learning ](/images/qlearn.png) 

Here, the policy that generates the data is a combination of the target policy and an \\(\epsilon \\) uniform policy and hance it is not an on-policy. 


![Figure 3: Q-learning ](/images/actor.png) 

Here the behavior policy and target policy are nominally the same, but the data generated from past versions of the policy stored in the buffer is used to update current policy. Use of past versions of policy is what makes this algorithm off-policy. In on-policy the data used to train must be generated by the current policy being at the moment updated. 

## 6.  Offline Reinforcement Learning 

On page 10: 

![Theorem 2.2 (DAgger error bound) ](/images/th22.png) 

One has to be careful what Levine is trying to convey here: 

##  Ideal‑case theorem (Ross et al., 2011, Thm 2.2)

If a policy’s classification error \\( \varepsilon \\) is measured on the **state distribution it induces itself** \\( d_{\pi} \\), then its task‑loss (or cost) obeys the linear‑in‑horizon bound

$$
\ell(\pi)\; \le\; C \;+\; H\,\varepsilon .
$$

This basically requires  that the policy we train using that policy's own data (and the oracle's labels), will not shift in distribution once unrolled. This is of course not feasible. 

---

##  What we really need (Ross et al., 2011, Thm 3.2)

DAgger is an *iterative* algorithm:

1. **Roll‑out** a mixed policy  
   $$
     \pi_i \;=\; \beta_i\,\pi^\star \;+\; (1-\beta_i)\,\hat{\pi}_i ,
   $$
   where \\( \beta_i \\) (decaying) is the probability of following the expert.

2. **Collect** visited states, query the expert for each state, and **aggregate** the data.

3. **Train** a new classifier \\( \hat{\pi}_{i+1} \\) on the growing dataset.

### Key bound on distribution mismatch  

$$
\bigl\lVert d_{\pi_i} - d_{\hat{\pi}_i}\bigr\rVert_1
\;\le\;
2\,H\,\beta_i .
$$

Because \\( \beta_i \to 0 \\), this mismatch shrinks over iterations.

### DAgger’s finite‑data guarantee  

For some policy in the sequence,

$$
\ell(\hat{\pi})
\;\le\;
C \;+\; H\,\varepsilon_N
\;+\;
O\!\bigl(\tfrac{\log H}{N}\bigr),
$$

where the \\( O(\cdot) \\) term captures  

* the controlled distribution mismatch \\( 2H\beta_i \\), and  
* online‑learning regret.

With a schedule such as \\( \beta_i \le 1/H \\) and \\( N = \tilde{O}(H) \\), the extra term vanishes, so DAgger **asymptotically achieves the same linear bound** proved in the ideal case.


Tutorial bundles:

1. the *ideal* bound above, and  
2. the fact that DAgger converges to it,

into one boxed statement, calling it “Theorem 2.2 (DAgger error bound).” 

Also note that in both 2.1 and 2.2 of the tutorial, the training is "pointwise". We are encountering state action pairs, we ignore the actions taken and train our model at each state based on the optimal action given by the oracle. We obviously cannot take those actions in supervised training. Also note that empirical risk minimization error is theoratical and over the entire distribution; hence the expectation 

$$\ell(\pi)\;=\;\mathbb{E}_{\tau \sim p_\pi}\!\Bigl[\;\sum_{t=0}^{H} \mathbf{1}\{\,a_t \neq a_t^\star\,\}\Bigr].$$

 We cannot obviously practically test that error on finite data. This is true about supervised training in general. Vapnik provides the statistical machinery that tells you how large a finite sample you need for your empirical loss to be a reliable proxy for that theoretical ε. Look up his text Statistical Learning Theory. 

Next we prove the doubly stochastic result:

![Figure 4: doubly stochastic result ](/images/double.png) 

 We want to prove that the **doubly robust estimator** recovers the true expected return \( J(\pi_\theta) \), i.e.:

$$
J(\pi_\theta) = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^H \gamma^t r_t \right]
$$

and that the DR estimator:

$$
\hat J_{\text{DR}} = \mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t \left(
w_t (r_t - \hat Q(s_t, a_t)) + w_{t-1} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)} \left[ \hat Q(s_t, a) \right]
\right)
\right]
$$

is equal to it in expectation, assuming the importance weights \( w_t = \prod_{k=0}^t \frac{\pi_\theta(a_k \mid s_k)}{\pi_\beta(a_k \mid s_k)} \) are correct.

---

### Step 1: Split the DR estimator

We split the estimator into two parts:

$$
\hat J_{\text{DR}} = 
\underbrace{\mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t w_t r_t \right]}_{\text{(A)}}
+ 
\underbrace{\mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t \left(
- w_t \hat Q(s_t, a_t) + w_{t-1} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)} \left[ \hat Q(s_t, a) \right]
\right)
\right]}_{\text{(B)}}
$$

---

### Step 2: Part (A) gives the true return

By per-decision importance sampling:

$$
\mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t w_t r_t \right]
= 
\mathbb{E}_{\tau \sim \pi_\theta} \left[
\sum_{t=0}^H \gamma^t r_t \right]
= J(\pi_\theta)
$$

---

### Step 3: Show that part (B) is zero

We analyze:

$$
\mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t \left(
- w_t \hat Q(s_t, a_t) + w_{t-1} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)} [\hat Q(s_t, a)]
\right)
\right]
$$

Use the identity:

$$
\mathbb{E}_{\tau \sim \pi_\beta} \left[ w_t \hat Q(s_t, a_t) \right]
= \mathbb{E}_{s_t \sim d^{\pi_\theta}_t, a_t \sim \pi_\theta(\cdot \mid s_t)} [\hat Q(s_t, a_t)]
$$

And similarly:

$$
\mathbb{E}_{\tau \sim \pi_\beta} \left[
w_{t-1} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)} [\hat Q(s_t, a)]
\right]
= \mathbb{E}_{s_t \sim d^{\pi_\theta}_t, a \sim \pi_\theta(\cdot \mid s_t)} [\hat Q(s_t, a)]
$$

Therefore, both terms are equal in expectation, and:

$$
\text{(B)} = 0
$$

---

### Final Conclusion

Since part (A) gives the true return and part (B) cancels out, the doubly robust estimator satisfies:

$$
\mathbb{E}[\hat J_{\text{DR}}] = J(\pi_\theta)
$$

under the assumption that the importance weights are correct.

Thus, the DR estimator is **unbiased** when either the model is correct or the importance weights are accurate.

