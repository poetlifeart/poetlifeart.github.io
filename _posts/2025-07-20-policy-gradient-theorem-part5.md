---
title: "Energy Models"
date: 2025-07-20
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-5/  
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




.................................
almost all modern IRL variants admit an energy-based model (EBM) view—especially MaxEnt / causal MaxEnt, Guided Cost Learning (GCL), AIRL, and adversarial imitation methods. Some classical margin/LP formulations are non-probabilistic but can be recovered as zero-temperature limits of EBMs.

Core EBM template. Define an energy \\( E_\theta(x) \\) over \\( x \\) (either a whole trajectory or a state–action item). The unnormalized density is \\( \exp(-E_\theta(x)) \\) with partition \\( Z(\theta) \\). Learning seeks \\( \theta \\) that makes expert data likely; methods differ by the choice of \\( x \\), the parameterization of \\( E_\theta \\), and how \\( Z(\theta) \\) (or its gradient) is handled.

$$
p_\theta(x)=\frac{\exp\!\big(-E_\theta(x)\big)}{Z(\theta)},\qquad
Z(\theta)=\int \exp\!\big(-E_\theta(x)\big)\,dx.
$$

MaxEnt / causal MaxEnt IRL (Ziebart ’08/’10). Energy is negative cumulative reward along a trajectory \\( \tau=(s_0,a_0,\dots,s_T) \\):

$$
E_\theta(\tau)=-\sum_{t=0}^{T} r_\theta(s_t,a_t),\qquad
p_\theta(\tau)\propto \exp\!\Big(\sum_{t=0}^{T} r_\theta(s_t,a_t)\Big).
$$

Partition handling via soft dynamic programming (log-sum-exp / soft Bellman backups).

Guided Cost Learning (GCL) / GAN-GCL (Finn et al. ’16). Use a learned trajectory score \\( f_\theta(\tau) \\) as energy:

$$
p_\theta(\tau)\propto \exp\!\big(f_\theta(\tau)\big),
$$

and estimate \\( \nabla_\theta \log Z(\theta) \\) with importance sampling from mixture samplers (policy rollouts). GAN-style contrastive training reduces variance—still an EBM view.

AIRL (Fu, Luo, Levine ’18). Constrain the discriminator log-odds (logit) to a reward+shaping form:

$$
\log\frac{D}{1-D}=f_{\theta,\phi}(s,a,s')=r_\theta(s,a)+\gamma\,h_\phi(s')-h_\phi(s).
$$

This is an implicit EBM over \\( (s,a,s') \\) with energy \\( -f_{\theta,\phi} \\). After training, \\( r_\theta \\) (ideally state-only) is a portable per-step energy/reward; \\( h_\phi \\) captures potential-based shaping.

GAIL (Ho \& Ermon ’16). Not IRL (no explicit reward recovery), but still EBM-flavored: minimize a divergence between expert and policy occupancies \\( \rho_E,\rho_\pi \\) via a discriminator whose log-odds estimates \\( \log \tfrac{\rho_E}{\rho_\pi} \\). That’s classic noise-contrastive estimation (NCE) / density-ratio learning—an implicit EBM with a learned sampler.

Feature-matching IRL (Abbeel \& Ng ’04). With a MaxEnt (Boltzmann) assumption one gets a linear-energy trajectory model

$$
E_w(\tau)=-\,w^\top \sum_t \phi(s_t,a_t),\qquad
p_w(\tau)\propto \exp\!\big(w^\top \Phi(\tau)\big).
$$

The classical margin/LP variant is the zero-temperature limit (\\( \beta\!\to\!\infty \\)) of this EBM.

Bayesian IRL / Boltzmann rationality. Local action likelihoods are EBMs,

$$
p(a\mid s,\theta)\propto \exp\!\{\beta\,Q^*_\theta(s,a)\},
$$

and the trajectory likelihood is their product across time given dynamics.

IQ-Learn / Soft Q-IRL / VICE-style methods. Soft Bellman operators make the per-state partition explicit via log-sum-exp:

$$
V(s)=\log \sum_{a} e^{\,Q(s,a)},
$$

again an energy view at the action level with a tractable local partition.

What is not automatically an EBM? Pure max-margin / LP IRL without an entropy (Boltzmann) assumption is non-probabilistic by construction, but can often be viewed as the zero-temperature limit of a corresponding MaxEnt EBM. Some inverse optimal control formulations that solve KKT/occupancy constraints directly can remain non-probabilistic unless a Boltzmann/MaxEnt link is added.

Why the EBM perspective helps: it unifies IRL as (contrastive) likelihood learning over expert data; clarifies why discriminators work (they estimate density ratios, i.e., log-energy differences); makes reward shaping explicit as adding potentials that do not change optima; and separates how methods handle the partition: DP (MaxEnt), importance sampling (GCL), or NCE/GAN (AIRL/GAIL).

Bottom line: with a finite-temperature (MaxEnt/Boltzmann) rationality assumption, virtually all IRL methods of practical interest admit an EBM view—trajectory-level or state–action level. The essential design choices are: the energy parameterization, the sampler, and how you estimate/avoid \\( Z(\theta) \\).



......................

MLE view of IRL (as EBMs). Given expert demonstrations \( \mathcal{D}=\{\tau_i\} \), model a trajectory density with energy (negative reward) and known dynamics:

$$
p_\theta(\tau)
\;\propto\;
p(s_0)\,\prod_{t} P(s_{t+1}\mid s_t,a_t)\,
\exp\!\Big(\sum_{t} r_\theta(s_t,a_t)\Big).
$$

Maximum likelihood estimates \( \theta \) by

$$
\max_\theta\; \sum_{\tau\in\mathcal D} \log p_\theta(\tau)
\;\;=\;\;
\mathbb E_{\tau\sim \mathcal D}\Big[\sum_t r_\theta(s_t,a_t)\Big]
\;-\;\log Z(\theta),
$$

where \( Z(\theta)=\sum_{\tau} p(s_0)\prod_t P(s_{t+1}\mid s_t,a_t)\exp(\sum_t r_\theta) \).
The gradient takes the standard EBM form

$$
\nabla_\theta \mathcal L(\theta)
\;=\;
\underbrace{\mathbb E_{\tau\sim \mathcal D}\Big[\sum_t \nabla_\theta r_\theta(s_t,a_t)\Big]}_{\text{data term}}
\;-\;
\underbrace{\mathbb E_{\tau\sim p_\theta}\Big[\sum_t \nabla_\theta r_\theta(s_t,a_t)\Big]}_{\text{model term}}.
$$

For linear rewards \( r_\theta(s,a)=\theta^\top \phi(s,a) \), this reduces to \(\nabla_\theta \mathcal L=\mathbb E_{\mathcal D}[\sum_t\phi]-\mathbb E_{p_\theta}[\sum_t\phi]\): match expert feature expectations (MaxEnt IRL).

Soft Bellman / local partition. The partition can be expressed via soft value functions:

$$
Q^\text{soft}(s,a)=r_\theta(s,a)+\gamma\,\mathbb E_{s'}[V^\text{soft}(s')],\qquad
V^\text{soft}(s)=\log\!\sum_a e^{Q^\text{soft}(s,a)}.
$$

Then the Boltzmann-rational policy is

$$
\pi_\theta(a\mid s)\;\propto\; \exp\!\big(Q^\text{soft}(s,a)-V^\text{soft}(s)\big),
$$

and MLE over trajectories is equivalent to entropy-regularized RL with reward \( r_\theta \).

Unknown/large dynamics \(\Rightarrow\) sampling estimators. The model expectation is intractable; use rollouts and importance sampling. Guided Cost Learning (GCL) constructs a mixture sampler \( \mu(\tau) \) (current policy + demo density estimate) and estimates

$$
\mathbb E_{\tau\sim p_\theta}[f(\tau)]
\;\approx\;
\frac{\mathbb E_{\tau\sim \mu}\big[w_\theta(\tau)\,f(\tau)\big]}{\mathbb E_{\tau\sim \mu}[w_\theta(\tau)]},
\qquad
w_\theta(\tau)\;\propto\;\frac{p_\theta(\tau)}{\mu(\tau)}\;\propto\;\frac{\exp(\sum_t r_\theta)}{\mu(\tau)}.
$$

This gives a practical MLE (forward KL) procedure with a learned sampler (policy optimization inner loop).

Adversarial / NCE view (AIRL). Rather than computing \( \log Z \), learn the density ratio by logistic regression (NCE):

$$
\log\frac{p_E(x)}{p_\theta(x)}\;\approx\;\log\frac{D(x)}{1-D(x)},
$$

and in AIRL constrain the logit

$$
\log\frac{D}{1-D}
\;=\;
f_{\theta,\phi}(s,a,s')
\;=\;
r_\theta(s,a)+\gamma\,h_\phi(s')-h_\phi(s),
$$

so the inner classifier approximates the likelihood ratio, while \( r_\theta \) is the portable reward. This is an implicit MLE surrogate: maximize a contrastive lower bound rather than the exact likelihood.

GAIL vs MLE. GAIL does \(\min_\pi 2\,\mathrm{JS}(\rho_E\|\rho_\pi)-\lambda\mathcal H(\pi)\) via a discriminator; it does \emph{not} maximize a trajectory likelihood (not an MLE), but its inner step is still density-ratio estimation. MLE corresponds to minimizing forward KL \( \mathrm{KL}(p_E\|p_\theta) \) (mode-covering); adversarial JS behaves differently (often less mode-covering).

IQ-Learn / soft Q IRL as MLE-like. Quadratic (e.g., \( \chi^2 \)) surrogates give practical convex objectives for the soft-Bellman-consistent \(Q\), e.g.

$$
\min_Q\;\; \mathbb E_{(s,a,s')\sim \mu_E}\!\big[\delta_Q(s,a)^2\big]\;+\;(1-\gamma)\,\mathbb E_{s_0\sim \rho_0}\!\Big[\log\sum_a e^{Q(s_0,a)}\Big],
\quad
\delta_Q(s,a)=Q(s,a)-\big(r(s,a)+\gamma\,\mathbb E_{s'}V(s')\big),
$$

which behaves like a regularized MLE in the soft-RL family.

Takeaways. MaxEnt/causal IRL is \emph{exactly} MLE of an EBM over trajectories/occupancies; GCL implements MLE with importance sampling; AIRL replaces explicit \( \log Z \) with contrastive (NCE/GAN) estimation of the likelihood ratio while structuring the logit to expose a reward; GAIL is not MLE but minimizes JS between occupancies. In all cases, the EBM/MLE lens clarifies the objective (forward KL vs JS), the partition handling (DP, IS, or NCE), and the role of the policy as a sampler.


................................

Goal: show how a standard maximum-likelihood estimation (MLE) problem can be written as an equivalent energy-based model (EBM) learning problem.

Start with any positive (possibly unnormalized) model
with base measure \\( b(x) \\) (independent of \\( \theta \\)) and score \\( s_\theta(x) \\):
 
$$
\tilde p_\theta(x) \;=\; b(x)\,\exp\!\big(s_\theta(x)\big).
$$
 
The normalized likelihood is
 
$$
p_\theta(x) \;=\; \frac{\tilde p_\theta(x)}{Z(\theta)},
\qquad
Z(\theta)=\int b(u)\,e^{s_\theta(u)}\,du.
$$
 
Define the energy
 
$$
E_\theta(x)\;:=\; -\,s_\theta(x)\;-\;\log b(x).
$$
 
Then \\( p_\theta \\) is exactly an EBM:
 
$$
p_\theta(x)\;=\;\frac{\exp\!\big(-E_\theta(x)\big)}{\displaystyle \int \exp\!\big(-E_\theta(u)\big)\,du}.
$$
 
Given data \\( \mathcal D=\{x_i\}_{i=1}^n \\), the MLE objective
 
$$
\mathcal L(\theta)\;=\;\frac{1}{n}\sum_{i=1}^n \log p_\theta(x_i)
$$
 
becomes, after substituting the EBM form,
 
$$
\mathcal L(\theta)
\;=\;
-\;\mathbb E_{x\sim \hat p_{\mathcal D}}\!\big[E_\theta(x)\big]
\;-\;\log Z(\theta),
$$
 
with gradient in canonical EBM form
 
$$
\nabla_\theta \mathcal L(\theta)
\;=\;
-\;\mathbb E_{x\sim \hat p_{\mathcal D}}\!\big[\nabla_\theta E_\theta(x)\big]
\;+\;
\mathbb E_{x\sim p_\theta}\!\big[\nabla_\theta E_\theta(x)\big].
$$
 
Exponential family / feature matching case. If \\( s_\theta(x)=\theta^\top \phi(x) \\), then
 
$$
E_\theta(x)= -\,\theta^\top \phi(x)-\log b(x),
\qquad
\nabla_\theta \mathcal L(\theta)=\mathbb E_{\hat p_{\mathcal D}}[\phi(x)]-\mathbb E_{p_\theta}[\phi(x)],
$$
 
i.e., MLE matches expert feature expectations (MaxEnt IRL special case).

Trajectory models (IRL). Let \\( x=\tau=(s_0,a_0,\dots,s_T) \\) and
 
$$
\tilde p_\theta(\tau)\;=\;\rho_0(s_0)\,\prod_{t=0}^{T-1} P(s_{t+1}\mid s_t,a_t)\;\exp\!\Big(\sum_{t=0}^{T-1} r_\theta(s_t,a_t)\Big).
$$
 
Here \\( b(\tau)=\rho_0(s_0)\prod_t P(s_{t+1}\mid s_t,a_t) \\) and \\( s_\theta(\tau)=\sum_t r_\theta(s_t,a_t) \\).
The energy is
 
$$
E_\theta(\tau)\;=\;-\sum_{t=0}^{T-1} r_\theta(s_t,a_t)\;-\;\log \rho_0(s_0)\;-\;\sum_{t=0}^{T-1}\log P(s_{t+1}\mid s_t,a_t),
$$
 
and the MLE gradient reduces to
 
$$
\nabla_\theta \mathcal L(\theta)
\;=\;
\mathbb E_{\tau\sim \hat p_{\mathcal D}}\!\Big[\sum_t \nabla_\theta r_\theta(s_t,a_t)\Big]
\;-\;
\mathbb E_{\tau\sim p_\theta}\!\Big[\sum_t \nabla_\theta r_\theta(s_t,a_t)\Big],
$$
 
since \\( b(\tau) \\) does not depend on \\( \theta \\). This is the MaxEnt IRL EBM view: learn \\( r_\theta \\) so that model feature sums match expert feature sums, with the partition handled via soft DP, sampling (GCL), or contrastive/NCE surrogates (AIRL).

Finally, MLE equals minimizing forward KL:
 
$$
\max_\theta \mathcal L(\theta)
\;\;\Longleftrightarrow\;\;
\min_\theta \mathrm{KL}\!\big(\hat p_{\mathcal D}\,\|\,p_\theta\big),
$$
 
which in the EBM view yields the same data-minus-model gradient above.
