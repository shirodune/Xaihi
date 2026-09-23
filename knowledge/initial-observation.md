---
type: Experiment
title: Initial observation experiment
description: Historical setup and screenshot results, with no gameplay or input claim.
status: draft
tags: [observation, windows, cua]
sources:
  - resource: Owner-authorized private setup session and tool outputs preceding repository creation
    title: Private execution evidence, not publicly distributed
---

# Goal

Establish whether an agent on a Linux host can observe an authorized Windows desktop through SSH and Cua Driver, without sending desktop input.

# Environment

- Agent runtime: Hermes; model label: gpt-6-astra.
- Target: Windows with an active interactive desktop.
- Driver: Cua Driver 0.28.2, standard permission mode.
- Transport: SSH over a private network.
- Observation: window metadata and PNG captures.
- Actions: read-only desktop inspection; no keyboard or mouse input.

# Observed results

- SSH identity and driver status queries succeeded after owner-assisted setup.
- A Cua process was observed in interactive Session 1.
- Window enumeration returned native application windows.
- A PowerShell window capture produced a nonblack, readable image.
- A full-display request timed out after 60 seconds. A later primary-display retry returned a normal 2560 x 1440 image in approximately 2.9 seconds for the remote command, excluding subsequent visual analysis.
- A Steam window capture showed the library. Its accessibility walk returned no actionable elements, so inspection used the image.
- The daemon reported that its authorization host was unavailable. Read observations succeeded; authorized input behavior remains untested.

# Human intervention

The owner installed and started Cua, configured Windows SSH, independently confirmed the host fingerprint, installed a dedicated public key, and authorized desktop inspection.

# Evidence and limitations

Results are summarized from actual tool outputs and image inspection in the setup conversation. Raw screenshots and transcripts are private and are not included. This public summary is not an independently reproducible run artifact, a success-rate measurement, or a gameplay result. No OKF verification event is asserted for this document.

No game control, completed game task, unattended play, or built-in Hermes remote computer-use routing was verified. The full-screen timeout's cause remains unknown. Re-test all connectivity and permissions in a new environment.
