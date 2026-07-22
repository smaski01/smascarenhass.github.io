---
layout: post
title: "Fixing spurious correlations in small-cohort validation"
---

One of the quieter risks in clinical ML work isn't the model architecture — it's what happens when you compute a Spearman correlation on a validation set of twelve infants and treat the result as if it were stable.

In the last pass through the Infant-GCN-PSN pipeline, three issues stood out:

## The problem with tiny validation sets

A Spearman rho computed on a handful of samples can swing wildly with the addition or removal of a single data point. Reporting it without a confidence interval — or without flagging the sample size — overstates how much the number actually tells you.

## Small-sample model comparison

Comparing two models' performance on a small test set using standard significance tests can produce misleading p-values. When `n` is small, routing to leave-one-out cross-validation (LOOCV) gives a more honest picture of generalization.

## Inter-rater agreement before averaging

When multiple clinicians score the same video, averaging their scores into a single composite label without first checking agreement (e.g. via weighted kappa) can bake in noise as if it were signal.

**The fix in v3:** config-level minimum-`n` thresholds that automatically route small datasets to LOOCV, plus an inter-rater agreement check that runs before any composite score is computed.

More on the implementation in a follow-up post.
