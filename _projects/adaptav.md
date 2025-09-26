---
layout: page
title: AdaptAV
description: Continuous adaptation of vision perception models for autonomous vehicles
importance: 1
category: research
related_publications: true
---

AdaptAV is a cloud-assisted framework that keeps autonomous driving perception models up to date in the field. We utilize lightweight, latency-aware models on the vehicle and periodically adapt them with data collected from new scenarios. A high-capacity oracle model runs in the cloud to label uploaded sensor data, guide the retraining process, and deliver improved models back to the fleet.

Key components of the system include:

- Cloud-hosted oracle models with access to specialized accelerators
- An on-vehicle data selection pipeline that prioritizes novel or low-confidence scenes
- Continuous deployment of refreshed models with version control and rollback support

The approach improves perception accuracy over time without sacrificing real-time constraints on the vehicle platform. See the publication below for more technical details.
