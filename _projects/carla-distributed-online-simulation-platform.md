---
layout: page
title: Carla1s Distributed Online Simulation Platform
description: Web-based remote driving lab with low-latency ROS video streaming
importance: 3
category: work
---

Built at Southern University of Science and Technology (2021-2022), this platform lets researchers drive Carla simulations from a browser without owning local GPUs.

### Highlights

- Delivered an HTTP streaming backend that forwards ROS image topics and Carla sensor feeds using HLS/WebRTC profiles, driving latency down from 1-2 seconds to ~50 ms.
- Automated environment provisioning with Ansible and Docker so instructors can spin up sandboxed lab instances on demand.
- Implemented session-aware access control and bandwidth monitoring to keep multi-user classes responsive during peak hours.

