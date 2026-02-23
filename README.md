# qTest + Tosca Cloud + GitHub Actions (Pulse)

This folder contains the **Tosca Cloud → JUnit → qTest Pulse** integration: a GitHub Actions workflow that runs a Tosca Cloud playlist, gets JUnit results, uploads them as an artifact, and sends them to qTest via Pulse.

**Designed for solution architects:** all configurable values use **secrets** and **variables** so you can adapt the workflow to your own Tosca/qTest/Pulse setup without editing code.
