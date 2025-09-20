---
title: "Offline RL-4"
date: 2025-07-18
categories:
  - policy-gradient
permalink: /policy-gradient-theorem/part-4/  
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




\documentclass[11pt]{article}
\begin{document}

Offline Q-Learning (Levine, 2020). Start from a fixed dataset of transitions \\((s,a,r,s')\\). Learn a critic \\(Q_\phi(s,a)\\) with Bellman backups:

$$
Q_\phi(s,a)\;\approx\; r \;+\; \gamma \max_{a'} \bar Q(s',a').
$$

A policy can be implicit (greedy w.r.t.\ \\(Q\\)) or explicit (actor--critic with regularization, e.g., CQL, IQL). The \\(\max\\) evaluates actions outside the dataset and induces extrapolation error; fixes include behavior regularization, conservative Q-learning, and expectile-based methods. No action generative model is required.

FlowQ (2025). Train a flow model on \\((s,a)\\) to learn \\(p_\mu(a\mid s)\\) (dataset-only). Train \\(Q\\) on \\((s,a,r,s')\\) with Bellman backups. The actor samples actions from the flow (staying in-distribution) and reweights/selects via \\(Q\\). FlowQ is strictly offline: needs only a dataset (with rewards), not expert demos or new rollouts.

Flow vs.\ Q (who trains when). Flow is dataset-only (can be pretrained or parallel). \\(Q\\) is reward-driven from \\((s,a,r,s')\\). The actor is where they meet: proposals from flow, selection by \\(Q\\).

Interaction requirements (corrected).
\begin{itemize}
\item Behavior Cloning (BC): needs expert demos; no environment interaction; no rewards.
\item Offline RL (CQL/IQL/etc.): needs a fixed dataset with reward labels; no new interaction.
\item FlowQ: needs a fixed dataset with rewards; no new interaction; no expert demos.
\item GAIL: needs expert demos and online rollouts; no environment rewards (discriminator provides signal).
\item AIRL: needs expert demos and online rollouts; no environment rewards (learned adversarial reward; portable \\(r_\theta\\)).
\item DAgger: needs expert demos and online rollouts; no rewards, but requires online expert queries/corrections.
\end{itemize}

Corrected table:

\begin{center}
\begin{tabular}{lccc}
\hline
\textbf{Algorithm} & \textbf{Needs expert demos?} & \textbf{Needs new states?} & \textbf{Needs env rewards?} \\
\hline
Behavior Cloning (BC)          & Yes                     & No                      & No \\
Offline RL (CQL / IQL / \dots) & No                      & No                      & Dataset rewards only \\
FlowQ                           & No (dataset only)       & No                      & Dataset rewards only \\
GAIL                            & Yes                     & Yes (rollouts)          & No (discriminator replaces reward) \\
AIRL                            & Yes                     & Yes (rollouts)          & \textbf{No} (learned adversarial reward; portable \\(r_\theta\\)) \\
DAgger                          & Yes                     & Yes (rollouts + queries)& No (expert corrections) \\
\hline
\end{tabular}
\end{center}

Discriminator-derived rewards in GAIL/AIRL. For a fixed discriminator \\(D\\), the policy term in the GAIL objective uses \\(\mathbb{E}_{\rho_\pi}[\log(1-D)]\\), so a natural per-step reward is

$$
r_{\mathrm{sat}}(s,a) = -\log\!\big(1 - D(s,a)\big).
$$

A common non-saturating alternative is the log-odds

$$
r_{\mathrm{logit}}(s,a) = \log \frac{D(s,a)}{1 - D(s,a)}.
$$

Both are strictly increasing in \\(D\\), hence share the same fixed point (matching occupancies). Their derivatives differ:

$$
\frac{d}{dD}\big[-\log(1-D)\big] = \frac{1}{1-D},
\qquad
\frac{d}{dD}\left[\log\frac{D}{1-D}\right] = \frac{1}{D(1-D)},
$$

so the logit reward provides stronger gradients when \\(D\\) is small. In AIRL, the discriminator logit is structured as \\(g(s,a,s')=r_\theta(s,a)+\gamma h(s')-h(s)\\), making \\(r_{\mathrm{logit}}\\) align with the portable reward \\(r_\theta\\).

\end{document}
