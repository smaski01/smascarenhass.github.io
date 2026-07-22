---
layout: post
title: "Rebuilding Infant-GCN-PSN for v3: what changed and why"
---

Infant-GCN-PSN started as a pose similarity network: extract joint positions with MediaPipe, compare movement patterns against clinician-labeled ground truth, and produce a similarity score. It worked — but v2's evaluation pipeline had three problems that only show up when you look closely at small-cohort clinical data.

## Problem 1: correlations that don't mean what they look like they mean

Computing a Spearman rho on a validation set of a dozen or so infants produces a number, but that number can shift substantially if even one sample changes. v2 reported these correlations without flagging how few samples they were based on.

## Problem 2: comparing models on tiny test sets

Standard significance testing assumes a reasonably sized sample. When comparing two model variants on a small held-out test set, ordinary tests can produce a misleadingly confident p-value.

## Problem 3: averaging scores before checking agreement

When more than one clinician reviews the same video, v2 averaged their scores into a single composite label. That's only valid if the reviewers actually agree with each other in the first place — something that was never checked before averaging.

## What v3 does differently

- **Minimum-n thresholds** in the config: any evaluation on a sample below the threshold is flagged rather than silently reported
- **Automatic LOOCV routing**: small datasets are routed to leave-one-out cross-validation instead of a standard train/test split
- **Inter-rater agreement checks**: composite scores are only computed after verifying reviewer agreement, rather than assuming it

None of these changes touch the model architecture — they're entirely about making sure the numbers coming out of the pipeline mean what they claim to mean. For clinical work, that matters more than squeezing out another point of accuracy.

Next up: benchmarking v3 against v2 on the same held-out cohort, and writing up the methodology section for submission.
