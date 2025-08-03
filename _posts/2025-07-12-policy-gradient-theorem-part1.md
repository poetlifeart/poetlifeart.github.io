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

Next we prove the doubly stochastic formualtion (6) on page 11 of the paper:

![Figure 4: doubly stochastic result ](/images/double.png) 

 We want to prove that the **doubly robust estimator** recovers the true expected return:

$$
J(\pi_\theta) = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^H \gamma^t r_t \right]
$$

First observe that the first sum above is Monte Carlo estimation  of the following expectation

$$
\hat J_{\text{DR}} = \mathbb{E}_{\tau \sim \pi_\beta} \left[
\sum_{t=0}^H \gamma^t \left(
w_t (r_t - \hat Q(s_t, a_t)) + w_{t-1} \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)} \left[ \hat Q(s_t, a) \right]
\right)
\right]
$$

which we will develop next. 

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

The result follows. Note the reason in the second part we have \\(w_{t-1}\\) and not \\(w_{t}\\) is that the
control action is averaged over and the result is a function of the state only. In the path \\(\tau\\) the state transition given an action does not depend on policies so in the importance sampling you can only adjust the sequence of policies that lead to a particular outcome. To compute this outcome all the probability actions up to and including \\(a_{t-1}\\) are multiplied by Markov property but not \\(a_{t}\\) . This is exactly the definition of \\(w_{t-1}\\).
Just to convince ourselves we give the details below:

Let 

$$ g(s_t) := \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)}[\hat Q(s_t,a)]$$

$$ w_{t-1} := \prod_{i=0}^{t-1}\frac{\pi_\theta(a_i\mid s_i)}{\pi_\beta(a_i\mid s_i)} $$.

### Step 1: Write the expectation as a sum over prefixes

$$
\begin{aligned}
\mathbb{E}_{\tau \sim \pi_\beta}\big[w_{t-1} \, g(s_t)\big]
&= \sum_{s_{0:t},\, a_{0:t-1}}
\rho_0(s_0)\,
\prod_{i=0}^{t-1}
\big[ \pi_\beta(a_i \mid s_i)\, T(s_{i+1}\mid s_i,a_i) \big]\,
\left( \prod_{i=0}^{t-1} \frac{\pi_\theta(a_i \mid s_i)}{\pi_\beta(a_i \mid s_i)} \right)
\, g(s_t) \\
&= \sum_{s_{0:t},\, a_{0:t-1}}
\rho_0(s_0)\,
\prod_{i=0}^{t-1}
\big[ \pi_\theta(a_i \mid s_i)\, T(s_{i+1}\mid s_i,a_i) \big]\,
g(s_t),
\end{aligned}
$$

since all \\( \pi_\beta(a_i \mid s_i) \\) cancel.

### Step 2: Recognize the resulting distribution

The remaining product is the prefix probability under \\( \pi_\theta \\). Marginalizing over the prefix up to time \\( t \\) yields \\( d_t^{\pi_\theta}(s_t) \\). Hence

$$
\mathbb{E}_{\tau \sim \pi_\beta}\big[w_{t-1} \, g(s_t)\big]
= \sum_{s_t} d_t^{\pi_\theta}(s_t)\, g(s_t)
= \mathbb{E}_{s_t \sim d_t^{\pi_\theta}}[g(s_t)].
$$

Now plug back $$( g(s_t) = \mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)}[\hat Q(s_t,a)] $$:

$$
\mathbb{E}_{\tau \sim \pi_\beta}
\Big[
w_{t-1}\,
\mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)}[\hat Q(s_t,a)]
\Big]
=
\mathbb{E}_{s_t \sim d_t^{\pi_\theta}}
\Big[
\mathbb{E}_{a \sim \pi_\theta(\cdot \mid s_t)}[\hat Q(s_t,a)]
\Big].
$$

By the law of total expectation \\( g(s_t)\\) is a function of \\(s_t\\)  only so we 
can always add expectation over action which will be integrated out), this equals

$$
\mathbb{E}_{s_t \sim d_t^{\pi_\theta},\, a \sim \pi_\theta(\cdot \mid s_t)}
\big[ \hat Q(s_t,a) \big].
$$

Next note there is a typo where  (Levine and Koltun, 2013) is refrenced; it should be:

$$
\nabla_{\theta} \,\bar J(\pi_{\theta}) \;\approx\;
\sum_{i=1}^{n} w_i
\Biggl(
  \sum_{t=0}^{H} \gamma^{t}\,
  \nabla_{\theta}\log\pi_{\theta}(a^{i}_{t}\mid s^{i}_{t})\;
  \hat A(s^{i}_{t},a^{i}_{t})
\Biggr)
\;+\;
\lambda\,\nabla_{\theta}\log\!\Bigl(\sum_{i=1}^{n} w_i\Bigr).
$$



### Approximate Off-Policy Policy Gradients

In this section observe that 

$$
J_{\pi_\beta}(\pi_\theta)
\;=\;
\mathbb{E}_{s \sim d_{\pi_\beta}}\!\bigl[V_{\pi}(s)\bigr]
$$

is a definition and not developed from first principles. It sort of works in practice. 

Also note  off-policy policy gradient (Degris et al., 2012):

$$
\mathbb{E}_{\substack{s \sim d_{\pi_\beta}(s)\\ a \sim \pi_\theta(a\mid s)}}\!
\Bigl[
   Q_{\pi_\theta}(s,a)\,\nabla_{\theta}\log\pi_\theta(a\mid s)
\Bigr]
$$



really hides an actor-critic type approach as we need to boothstrap \\( Q \\) function here. \\( Q \\) itself
requires target policy data which we don't have but something like this might work using the off-line data: 


1. **Initialisation**  
   Choose a differentiable function class  
   $$\{\,Q^{\pi_{\theta}}_{\phi}(s,a)\mid \phi\in\Phi\}$$  
   (e.g. a neural network) and set the critic parameters  
   $$\phi_{0}\gets \text{RandomInit}().$$  

2. **Iterative Bellman regression** for \\(k = 1,2,\dots,K\\):

   1. **Target construction**  
      For every logged transition \\((s_i,a_i,r_i,s'_i)\\) define  
      $$y_i \;=\; r_i + \gamma\,Q^{\pi_{\theta}}_{\phi_{k-1}}\bigl(s'_i,\,\pi_{\theta}(s'_i)\bigr).$$

   2. **Supervised fit**  
      Update the critic by least‑squares regression  
      $$\phi_k \;=\;
        \arg\min_{\phi\in\Phi}\;
        \frac{1}{|\mathcal D|}\sum_{i}
        \bigl(Q^{\pi_{\theta}}_{\phi}(s_i,a_i)-y_i\bigr)^2.$$

3. **Return the critic for the current policy**  
   $$Q^{\pi_{\theta}}_{\phi_{K}}(s,a)\;\approx\;Q^{\pi_{\theta}}(s,a)$$  
   and the state‑value estimate  
   $$C_{\pi_{\theta}}(s)\;\approx\;Q^{\pi_{\theta}}_{\phi_{K}}\bigl(s,\pi_{\theta}(s)\bigr).$$  

All targets and losses use only the fixed offline batch  
$$ \mathcal D = \{(s_i,a_i,r_i,s'_i)\} $$ and the current policy  
$$ \pi_{\theta} $$; no fresh interaction with the environment is required.


### Marginalized Importance Sampling

In this section, we give more details for how (8) relates to (7); it does not follow from it as it might seem implied here.

**From (7) to a coordinate‑wise SA, and why it is not (8)**

**Forward balance**
$$
d_\beta(s')\,\rho(s')
\;=\;(1-\gamma)\,d_0(s')
\;+\;\gamma\sum_{s,a} d_\beta(s)\,\rho(s)\,\pi(a\mid s)\,T(s'\mid s,a).
\quad \text{(7)}
$$

Divide by \\(d_\beta(s')\\) (do *not* move it inside the sum):
$$
\rho(s')
\;=\;(1-\gamma)\,\frac{d_0(s')}{d_\beta(s')}
\;+\;\frac{\gamma}{d_\beta(s')}
\sum_{s,a} d_\beta(s)\,\rho(s)\,\pi(a\mid s)\,T(s'\mid s,a).
\quad \text{(*)}
$$

$$
\rho(s') \;=\; (1-\gamma)\,\frac{d_0(s')}{d_\beta(s')} \;+\; \frac{\gamma}{d_\beta(s')}\,\mathbb{E}_{\substack{S\sim d_\beta\\ A\sim \pi_\beta(\cdot\mid S)}}\!\left[ \frac{\pi(A\mid S)}{\pi_\beta(A\mid S)}\,\rho(S)\,T(s'\mid S,A) \right].
$$


**Coordinate‑wise stochastic approximation of (*)**  
Given a logged tuple \\((S,A,S')\\) with \\(S\sim d_\beta\\), \\(A\sim \pi_\beta(\cdot\mid S)\\), \\(S'\sim T(\cdot\mid S,A)\\), update only the observed coordinate \\(s'=S'\\):
$$
\rho(S')\ \leftarrow\ \rho(S')\ +\ \alpha\Big[
(1-\gamma)\,\frac{d_0(S')}{d_\beta(S')}
\;+\;\frac{\gamma}{d_\beta(S')}\,\frac{\pi(A\mid S)}{\pi_\beta(A\mid S)}\,\rho(S)
\;-\;\rho(S')\Big].
$$

Here, \\(T(s'\mid s,a)\\) is handled by sampling \\(S'\\); the second factor \\(1/d_\beta(S')\\) is *kept* exactly as in (*). This is the direct coordinate‑wise SA for (7).

**What Eq. (8) does instead (and why it is not derived from (7)).**  
The practical update used in the literature is
$$
\rho(S') \ \leftarrow\ \rho(S') \ + \ \alpha\Big[
(1-\gamma) \ + \ \gamma\,\tfrac{\pi(A\mid S)}{\pi_\beta(A\mid S)}\,\rho(S) \ - \ \rho(S')\Big].
\quad \text{(8)}
$$
Compared to the SA of (7), Eq. (8) replaces the state‑dependent term \\((1-\gamma)\,\tfrac{d_0(S')}{d_\beta(S')}\\) by the constant \\((1-\gamma)\\), and drops the explicit \\(1/d_\beta(S')\\) from the second term. Therefore, Eq. (8) is **not** a recursion of (7); it corresponds to a surrogate “reset‑to‑behavior” operator. It is chosen because it is a \\(\gamma\\)‑contraction and works well empirically. When \\(d_0=d_\beta\\), the first terms coincide, but the second‑term difference remains. In fact, C. Gelada, M. Bellemare (2019), make it clear (7) is a *definition*. 

Not every equation gives rise to a recursive solution obviously and here \\(d_\beta(s')\\) inverse can be a large number. So why does dropping it work? C. Gelada, M. Bellemare (2019) show it is meaningful, has nice properties and works in practice. A rough idea might be that from the point of view of sample paths, we could drop \\(d_\beta(s')\\) if we could draw according to it. We are; however, already drawing  \\(s'\\) conditionally correctly and can assume we are also drawing it according to \\(d_\beta(s')\\) after some iterations, so it was sort of there and got cancelled by dividing by \\(d_\beta(s')\\). In other words we are assuming we are drawing approximately according to \\(d_\beta(s')\\) already even though we are not. 



### 7) Backward Bellman equation based approaches via convex duality 

## A Full, Self-Contained Proof of the DualDICE Objective

This note provides a clean, rigorous derivation showing how the off-policy density-ratio correction objective (equation 6) admits a unique solution, and how the change of variables to \\(x\\) (equation 8) makes it practically computable from data.

### 1. Setting and Definitions
- **Spaces and measures**: Let \\((S, A)\\) be the state–action space. Define:
  - \\(d_D(s,a)\\): the known behavior-data distribution (assumed \\( (d_\pi \ll d_D) \\).
  - 
    $$
    d_\pi(s,a) = (1-\gamma)\sum_{t=0}^\infty \gamma^t\bigl[\beta_t(s)\,\pi(a\mid s)\bigr],
    $$

    the unnormalized visitation measure under \\(\pi\\), starting from \\(\beta_0=\beta\\).

- **Bellman-transition operator** \\(B^\pi: L^2(d_D)\to L^2(d_D)\\):
  $$
    (B^\pi\nu)(s,a) = \gamma\,\mathbb{E}_{s'\sim T(s,a),\,a'\sim\pi(\cdot\mid s')}\bigl[\nu(s',a')\bigr].
  $$
  Since \\(\|B^\pi\|\le\gamma<1\\), the operator \\(I - B^\pi\\) is invertible on \\(L^2(d_D)\\) with bounded inverse.

### 2. The Original Objective (Equation 6)
We work in the Hilbert space \\(\mathcal H = L^2(d_D)\\) with inner product
$$
\langle f,g\rangle = \int_{S\times A} f(s,a)\,g(s,a)\,d_D(s,a).
$$

Define:
$$J(\nu) = \tfrac12\,\mathbb{E}_{d_D}\bigl[(\nu - B^\pi\nu)^2\bigr] - (1-\gamma)\,\mathbb{E}_{\beta,\pi}[\nu],$$
where

$$
\mathbb{E}_{\beta,\pi}[\nu]
= \int_{S\times A} \nu(s,a)\,\bigl[\beta(s)\,\pi(a\mid s)\bigr]\,ds\,da.
$$

1. **Convexity and coercivity:** the quadratic term is strictly convex and coercive (since \\(\|I - B^\pi\|>0\\)); the second term is a bounded linear functional.  
2. **Existence & uniqueness:** by standard Hilbert-space theory, \\(J(\nu)\\) has a unique minimizer \\(\nu^*\\). The Euler–Lagrange condition is

   $$
   (I - B^\pi)^*(I - B^\pi)\,\nu^* = (1-\gamma)\,\tfrac{d_\pi}{d_D},
   $$

   so formally

   $$
   \nu^* = (I - B^\pi)^{-1}\Bigl[(1-\gamma)\,\tfrac{d_\pi}{d_D}\Bigr].
   $$

   This form is not directly computable from data due to nested expectations and unknown \\(d_\pi\\).

### 3. Change of Variables to \\(x\\)
Define

$$
x(s,a) := (I - B^\pi)\nu(s,a)
         = \nu(s,a) - (B^\pi\nu)(s,a).
$$
Since \\(\|B^\pi\|<1\\), \\(I - B^\pi\\) is invertible on \\(L^2(d_D)\\), giving

$$
x \longleftrightarrow \nu = (I - B^\pi)^{-1} x,
\quad
\|x\|_{d_D}<\infty \iff \|\nu\|_{d_D}<\infty.
$$

Substitute into \\(J(\nu)\\) to obtain the quadratic in \\(x\\) (equation 8):
$$
J_1(x) = \tfrac12\,\mathbb{E}_{d_D}[x^2] - \mathbb{E}_{d_\pi}[x].
$$

Minimizing \\(J_1\\) over \\(x\\) is equivalent to minimizing \\(J\\) over \\(\nu\\).

### 4. Telescoping-Sum Derivation 
$$(1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}
\left[
\nu(s,a)-\gamma\,\mathbb{E}_{s'\sim T(s,a),a'\sim\pi(s')}[\nu(s',a')]
\right]$$

Next, separate into two sums, clearly keeping placeholders:

$$=(1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}[\nu(s,a)]
-(1-\gamma)\sum_{t=0}^{\infty}\gamma^{t+1}\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}\left[
\mathbb{E}_{s'\sim T(s,a),a'\sim\pi(s')}[\nu(s',a')]
\right]$$

Bring the expectation explicitly inside and introduce indexing clearly:

$$=(1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}[\nu(s_t,a_t)]
-(1-\gamma)\sum_{t=0}^{\infty}\gamma^{t+1}\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t),s_{t+1}\sim T(s_t,a_t),a_{t+1}\sim\pi(s_{t+1})}[\nu(s_{t+1},a_{t+1})]$$

Shift indices explicitly in the second sum (\(t'=t+1\)):

$$=(1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}[\nu(s_t,a_t)]
-(1-\gamma)\sum_{t'=1}^{\infty}\gamma^{t'}\,\mathbb{E}_{s_{t'}\sim\beta_{t'},a_{t'}\sim\pi(s_{t'})}[\nu(s_{t'},a_{t'})]$$

Rename index \(t'\) back to \(t\):

$$=(1-\gamma)\sum_{t=0}^{\infty}\gamma^t\,\mathbb{E}_{s_t\sim\beta_t,a_t\sim\pi(s_t)}[\nu(s_t,a_t)]
-(1-\gamma)\sum_{t=1}^{\infty}\gamma^{t}\,\mathbb{E}_{s_{t}\sim\beta_{t},a_{t}\sim\pi(s_{t})}[\nu(s_{t},a_{t})]$$

Explicit telescoping clearly leaves only the first term:

$$=(1-\gamma)\mathbb{E}_{s_0\sim\beta_0,a_0\sim\pi(s_0)}[\nu(s_0,a_0)]$$

Finally, noting \(\beta_0=\beta\):

$$(1-\gamma)\mathbb{E}_{s\sim\beta,a\sim\pi(s)}[\nu(s,a)]$$


### 5. Existence and Closed-Form Solution for \\(x\\)
On \\(L^2(d_D)\\),

$$
J_1(x) = \tfrac12\int x^2\,d_D - \int x\,d_\pi
$$

is strictly convex. Its first-order condition at \\(x^*\\) is:

$$
0 = \int x^*\,h\,d_D - \int h\,d_\pi,\quad \forall h\in L^2(d_D).
$$

Assuming \\(d_\pi\ll d_D\\), write \\(d_\pi = w_{\pi/D}\,d_D\\). Then

$$
\int[x^*(s,a)-w_{\pi/D}(s,a)]\,h(s,a)\,d_D(s,a) = 0\;\forall h\;\Longrightarrow\;x^*(s,a) = w_{\pi/D}(s,a).
$$

### 6. Recovering \\(\nu^*\\) via Bellman-Residual Definition
Finally, define \\(\nu^*\\) by

$$
\nu(s,a) = x^*(s,a) + \gamma\,\mathbb{E}_{s'\sim T(s,a),\,a'\sim\pi}[\nu(s',a')],
$$

which uniquely defines a bounded solution in \\(L^2(d_D)\\). By construction,
$$
(\nu^* - B^\pi\nu^*)(s,a) = x^*(s,a) = \frac{d_\pi(s,a)}{d_D(s,a)}.
$$

---




$$
J(\nu) = \frac{1}{2}\mathbb{E}_{(s,a)\sim d_D}\left[(\nu(s,a)-\gamma \mathbb{E}_{s',a'\sim T\pi}[\nu(s',a')])^2\right] - (1-\gamma)\mathbb{E}_{s\sim\beta,a\sim\pi(s)}[\nu(s,a)]
$$

- Parameterization  
Choose a differentiable function class \\(\nu_\theta(s,a)\\).

- Empirical loss  

$$
\hat J(\theta)
= \frac{1}{2N}\sum_{i=1}^N
\Bigl[
\nu_\theta(s_i,a_i)
- \gamma\,\nu_\theta(s'_i,a'_i)
\Bigr]^2
\;-\;
\frac{1-\gamma}{M}\sum_{j=1}^M \nu_\theta(\hat s_j,\bar a_j).
$$

- Stochastic gradient, Define

$$
\delta_i \;=\;\nu_\theta(s_i,a_i)\;-\;\gamma\,\nu_\theta(s'_i,a'_i).
$$

Then over a minibatch of size \\(B\\):

$$
g_{\rm batch}
=\frac{1}{B}\sum_{i=1}^B
\Bigl[
\delta_i\,\nabla_\theta\nu_\theta(s_i,a_i)
\;-\;\gamma\,\delta_i\,\nabla_\theta\nu_\theta(s'_i,a'_i)
\Bigr]
\;-\;
\frac{1-\gamma}{B}\sum_{j=1}^B\nabla_\theta\nu_\theta(\hat s_j,\bar a_j).
$$

- Gradient step  

$$
\theta \;\leftarrow\;\theta\;-\;\alpha\,g_{\rm batch}.
$$



$$
\max_{\pi} \ \mathbb{E}_{s \sim d^{\pi}, \ a \sim \pi(\cdot|s)}
\big[ r(s,a) \big]
- \alpha \, D_f\!\big( d^{\pi}(s,a), \ d^{\pi_\beta}(s,a) \big)
$$

Using the variational dual form of the \\(f\\)-divergence:

$$
D_f(p,q) = \max_{x : S \times A \to \mathbb{R}}
\ \mathbb{E}_{y \sim p}\big[ x(y) \big]
- \mathbb{E}_{y \sim q} \big[ f^*( x(y) ) \big]
$$

and applying the change of variables

$$
Q(s,a) = \mathbb{E}_{s' \sim T(s,a),\ a' \sim \pi(\cdot|s')}
\left[ r(s,a) - \alpha \, x(s,a) + \gamma \, Q(s',a') \right]
$$

we obtain the saddle-point optimization:

$$
\begin{aligned}
& \max_{\pi} \ \min_{Q} \ L(Q, \pi_\beta, \pi) \\
&= \mathbb{E}_{s_0 \sim d_0, \ a \sim \pi(\cdot|s_0)} 
\big[ Q(s_0,a) \big] \\
&\quad + \alpha \,
\mathbb{E}_{(s,a) \sim d^{\pi_\beta}}
\left[
f^*\!\left(
\frac{
r(s,a) 
+ \gamma \ \mathbb{E}_{s' \sim T(s,a),\ a' \sim \pi(\cdot|s')} 
\big[ Q(s',a') \big] 
- Q(s,a)
}{\alpha}
\right)
\right]
\end{aligned}
$$


When \\(f(x) = x^2\\), \\(f^*(x) = x^2\\), this reduces to a standard actor–critic objective plus an additional regularization term on \\(Q\\)-values at the initial state.

In the parametric form:

-Actor: 
\\(\pi_\theta(a|s)\\), with parameters \\(\theta\\) updated via

$$
\nabla_\theta L(\theta) = 
\mathbb{E}_{s \sim d^{\pi_\theta}, \ a \sim \pi_\theta}
\left[ \tilde{Q}^{\pi_\theta}(s,a) \ \nabla_\theta \log \pi_\theta(a|s) \right]
$$

- Critic: \\(Q_\phi(s,a)\\), with parameters \\(\phi\\) updated by

$$
\min_{\phi} \ L(Q_\phi, \pi_\beta, \pi_\theta)
$$

The actor and critic are optimized alternately until convergence.

.....................

$$
\begin{aligned}
& \max_{\pi} \ \min_{Q} \ L(Q, \pi_\beta, \pi) \\
&= \mathbb{E}_{s_0 \sim d_0, \ a \sim \pi(\cdot|s_0)} 
\big[ Q(s_0,a) \big] \\
&\quad + \alpha \,
\mathbb{E}_{(s,a) \sim d^{\pi_\beta}}
\left[
f^*\!\left(
\frac{
r(s,a) 
+ \gamma \ \mathbb{E}_{s' \sim T(s,a),\ a' \sim \pi(\cdot|s')} 
\big[ Q(s',a') \big] 
- Q(s,a)
}{\alpha}
\right)
\right]
\end{aligned}
$$

We parameterize the critic as \\( Q_\phi(s,a) \\) and the actor as \\( \pi_\theta(a|s) \\).  
The optimization alternates between minimizing over \\( Q_\phi \\) and maximizing over \\( \pi_\theta \\).

**Critic update** (for fixed \\( \pi_\theta \\)):

$$
\phi \leftarrow \phi - \eta_Q \, \nabla_\phi \;
\mathbb{E}_{s_0 \sim d_0, \ a_0 \sim \pi_\theta} \big[ Q_\phi(s_0, a_0) \big]
+ \alpha \,
\mathbb{E}_{(s,a) \sim \mathcal{D}}
\left[
f^*\!\left(
\frac{
r(s,a) 
+ \gamma \, \mathbb{E}_{a' \sim \pi_\theta(\cdot|s')}[ Q_\phi(s',a') ]
- Q_\phi(s,a)
}{\alpha}
\right)
\right]
$$

**Actor update** (for fixed \\( Q_\phi \\)):

$$
\theta \leftarrow \theta + \eta_\pi \;
\mathbb{E}_{s,a \sim \pi_\theta}
\left[ Q_\phi(s,a) \, \nabla_\theta \log \pi_\theta(a|s) \right]
$$

