---
layout: page
title: On-the-fly Coding of Vision Inputs for Evidence-Preserving Perception
description: Middleware that losslessly archives autonomous vehicle sensor evidence while meeting real-time budgets
importance: 2
category: work
---

This research prototype ships an evidence-preserving perception stack for autonomous driving platforms at North Carolina State University (2023-2024).

### Highlights

- Designed a capture-to-storage pipeline that records every camera frame alongside derived metadata so perception experiments remain reproducible.
- Applied adaptive JPEG and H.264 compression balanced by scene entropy, then offloaded encoding to NVENC/NVDEC engines to maintain real-time throughput on Jetson-class hardware.
- Exposed V4L2- and ROS-friendly interfaces so downstream autonomy modules can replay ground-truth sequences without format conversions.

