# Detection Agent

Automate the feedback loop between Google SecOps SIEM (lagging indicators) and Project Harimau (forward threat indicators).

## Overview
This repository contains the detached Detection Agent architecture (formerly Phase 7 of Project Harimau). The agent is decoupled from the Project Harimau monolithic repository. 

It receives an optional `HTTP-JSON-RPC` push webhook via Project Harimau's Lead Hunter to support this architecture, controlled entirely by environment variables.
