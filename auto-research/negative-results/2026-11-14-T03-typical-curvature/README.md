# Pointwise Bakry–Émery curvature is negative on typical Wilson configurations

## Metadata

- Result ID and date: `2026-11-14-T03-typical-curvature`; 2026-11-14.
- Author: Rohan Arun, with Claude (Anthropic) assistance in a Claude Code session.
- Tracks: T03-D primary; T04 and T06 related.
- Outcome: **negative result for a specific untried variant of the geometric route.** A probabilistic version of the curvature argument ("positive curvature on typical configurations, with the atypical set controlled separately") fails at the coupling range that matters. No mass-gap estimate.
- Evidence: Monte Carlo and floating-point linear algebra on $3^4$ and $4^4$ tori. Exact Hessian, validated against finite differences. No proof, no Lean, no independent review.
- Repository base commit: `ff784a0`.
- Toolchain: Python 3.9.6, numpy 2.0.2, scipy 1.13.1.
- Builds on: [curvature scope](../2026-11-12-T03-bakry-emery-scope/README.md), which bounded the worst case. This record measures the typical case.

## Question and claim tested

The earlier scope result shows the Bakry–Émery condition holds for every configuration only when $\beta<1/[4(d-1)]$. It left open whether the curvature is positive for *typical* configurations at larger $\beta$, in which case a probabilistic argument (curvature on a high-probability set plus control of its complement) would reach further.

Setting: bare SU(2) Wilson measure $\exp(\beta\sum_p\mathrm{Re}\,U_p)$, $\mathrm{Re}\,U_p=1-\mathrm{price}$, $d=4$, periodic $L^4$ torus; repository $\beta$ equals the usual $\beta_{\rm std}$. Each link carries the round unit $S^3$ ($\mathrm{Ric}=2$). For sampled configurations $U$ compute the lowest eigenvalues of the matrix $\mathrm{BE}(U)=2I+\beta\,\mathrm{Hess}\sum_p\mathrm{price}$.

Tested claim: $\lambda_{\min}\mathrm{BE}(U)\geq\rho>0$ for typical $U$, uniformly in $\beta$.

Falsification: typical $\lambda_{\min}$ at or below zero in the coupling range of interest.

## Method

- Metropolis sampling, 150 thermalization sweeps, 15 sweeps between samples, 3 hits per link, Gaussian Lie-algebra proposals with width $\min(1.2,\,1.4/\sqrt{\beta|S|})$ ($S$ the staple sum). Cold start for $\beta\geq2$, hot start below. Seeds $1000+i$.
- The Hessian is assembled exactly from quaternion algebra (as in the scope record, now vectorized and sparse) and the lowest six eigenvalues come from ARPACK. Validation: the assembled Hessian agrees with Richardson-extrapolated finite differences to $9.9\times10^{-9}$ (relative) on a $3^3$ torus, and the staple differential matches to $3\times10^{-16}$.
- Sampler sanity check against known behaviour: mean plaquette $0.060,\,0.123$ at $\beta=0.25,\,0.5$ (strong-coupling expansion gives about $\beta/4$) and $0.871,\,0.901$ at $\beta=6,\,8$ (weak-coupling expansion about $1-3/(4\beta)$). This is not a precision test of equilibrium.

## Results ($4^4$ torus, six decorrelated samples per $\beta$; $3^4$ torus, eight samples)

| $\beta$ | mean plaquette | lowest eigenvalue of BE, mean ($4^4$) | worst sample ($4^4$) | $3^4$ mean | lowest eigenvalue of $\mathrm{Hess}$, mean ($4^4$) |
| --- | --- | --- | --- | --- | --- |
| 0.25 | 0.060 | −0.014 | −0.043 | −0.002 | −8.06 |
| 0.5 | 0.123 | −1.86 | −1.92 | −1.74 | −7.71 |
| 1 | 0.235 | −5.12 | −5.33 | −4.76 | −7.12 |
| 2 | 0.491 | −9.01 | −9.70 | −8.10 | −5.51 |
| 4 | 0.797 | −9.26 | −9.61 | −9.36 | −2.81 |
| 8 | 0.901 | −12.20 | −13.17 | −11.19 | −1.78 |

Full table with intermediate couplings and second-lowest eigenvalues in [out_L4.json](out_L4.json) and [out_L3.json](out_L3.json).

Observations, all at these sizes only:

1. **Negative everywhere above $\beta\approx0.25$.** The zero crossing is at about $\beta\approx0.25$ (where $2+\beta\lambda_{\min}(\mathrm{Hess})\approx2-0.25\times8$), about three times the analytic worst-case threshold $1/12$ and consistent with the earlier local-search estimate $\approx0.22$. At $\beta=0.5$ the minimum is already $-1.9$.
2. **Typical is almost as bad as the worst case.** The lowest Hessian eigenvalue of a typical configuration at $\beta=0.25$ is $-8.01$ on a $3^4$ torus, close to the lowest value found by adversarial search on the same torus in the scope record ($-8.9$). The negative direction is not rare.
3. **It is a band, not an outlier.** The second-lowest BE eigenvalue is nearly as negative as the lowest (e.g. $-11.7$ against $-12.2$ at $\beta=8$).
4. **It worsens with coupling.** $\lambda_{\min}\mathrm{BE}$ falls from $-1.9$ at $\beta=0.5$ to $-12.2$ at $\beta=8$, while the Hessian's own minimum shrinks only from $-7.7$ to $-1.8$, so $\beta\lambda_{\min}$ wins.
5. **Mild volume dependence.** $3^4$ and $4^4$ agree to about 10%, with $4^4$ slightly more negative at most couplings.

## Interpretation (hypothesis, not tested here)

At identity, $\mathrm{Hess}\sum_p\mathrm{price}$ is positive semidefinite (it is the lattice curl-curl operator), so the negative eigenvalues come from the deviation of $U_p$ from the identity. I expect the leading effect to be the non-abelian commutator term of the second variation of the Yang–Mills action in a fluctuating background field strength, the same structure behind the known negative modes of the Hessian in chromomagnetic backgrounds (Nielsen–Olesen type instabilities). That is a physics expectation recalled from the literature, not checked against sources or against these data; a clean scaling law of $\lambda_{\min}(\mathrm{Hess})$ with $\sqrt{1-\langle P\rangle}$ was **not** found (the ratio drifts from $-8$ to $-5.6$ between $\beta=1$ and $\beta=8$).

## What this rules out and what it does not

Rules out, at these sizes: a probabilistic Bakry–Émery argument for the bare Wilson measure that relies on positive curvature on typical configurations at $\beta\gtrsim0.3$. Since the notebook records the endpoint coupling $\mathrm{bar}>1$, the range that matters is covered.

Does not rule out: any non-pointwise geometric mechanism. Pointwise curvature is a sufficient condition for a Poincaré inequality, not a necessary one; the bare measure presumably has a gap at these couplings. The routes that remain untested here are a multiscale Bakry–Émery criterion applied to a fluctuation field after a Gaussian-type decomposition (the structure used for the sine-Gordon model in the work cited in the [posterior-curvature record](../../general/2026-11-13-T03-geometric-posterior-curvature/README.md) on its branch), curvature of a gauge-quotient metric, and curvature of the ground-state-weighted measure. None was attempted.

Not checked: infinite volume; larger tori; longer thermalization; other gauge groups; the generated coarse measures.

Triage note, an opinion rather than a result: I could not identify a mechanism by which an $E_8$ structure would enter a four-dimensional $SU(N)$ estimate. The exceptional four-dimensional lattice is $D_4$ (the Hurwitz quaternions), and $E_8$ reaches $SU(2)$ only through the McKay correspondence for the binary icosahedral subgroup, which gives a finite-group approximation rather than a new estimate. Symmetry of the spacetime lattice does not address the dynamical obstruction recorded elsewhere in this notebook.

## Reproduction

```sh
python3 auto-research/negative-results/2026-11-14-T03-typical-curvature/check_typical_curvature.py validate
python3 auto-research/negative-results/2026-11-14-T03-typical-curvature/check_typical_curvature.py run 4 0.25,0.5,1,1.5,2,2.5,3,4,6,8 6 150 15 out_L4.json
python3 auto-research/negative-results/2026-11-14-T03-typical-curvature/check_typical_curvature.py run 3 0.25,0.5,1,2,4,8 8 150 15 out_L3.json
```

The $4^4$ run took about 215 seconds on 10 parallel processes; the $3^4$ run was faster. [Receipt](verification.json). numpy printed spurious matmul warnings in the validation step; all reported values are finite and the finite-difference agreement is the check that matters.

## Failure lesson

Pointwise convexity of the Wilson action's configuration space is not the mechanism behind spatial mixing at intermediate coupling: negative curvature directions are typical, not rare. Before building a probabilistic argument on a curvature bound, measure the typical spectrum.

## Track contributions and next experiment

- T03-D: the typical-configuration variant of the curvature route is closed at $\beta\gtrsim0.3$.
- T06: a recorded scope result for the geometric family.

Next bounded experiment, if the geometric theme continues: compute the same spectrum **after projecting out gauge directions and restricting to horizontal directions** (the metric of the quotient), to test whether the negative band lies in physical or gauge directions. Success criterion: horizontal-subspace $\lambda_{\min}$ either positive at all sampled $\beta$ (reopening the quotient route) or negative with the same magnitude (closing it). Cost: one more projection step in the existing script.

## Review and correction history

- 2026-11-14: created. Single AI session, small samples, no independent review.
