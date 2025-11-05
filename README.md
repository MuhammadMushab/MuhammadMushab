# ██████╗  ██╗  ██████╗   _P I C O  H A C K E R_  
> ⚫ Tiny, powerful cyber toolbox — CTF helpers · pentest demos · learning labs

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Language](https://img.shields.io/badge/lang-Python%20%7C%20Bash-black.svg)]
[![Build](https://img.shields.io/badge/ci-passing-brightgreen.svg)]
[![Docker Image](https://img.shields.io/badge/docker-ready-blue.svg)]
[![Contributors](https://img.shields.io/github/contributors/mushabmuchu/pico-hacker.svg)]

---

<!-- Animated demo GIF (generate and place at docs/screenshots/demo.gif) -->
<p align="center">
  <img src="docs/screenshots/demo.gif" alt="pico-hacker demo" width="820" />
</p>

---

## 🔥 Quick Project Summary
**Pico Hacker** — a compact toolkit for CTFs, pentest demos and learning labs.  
This repo emphasizes readable PoCs, repeatable labs, and a slick README with animated demos.

---

## 📚 Contents
- **Introduction** — this file (README)
- **/tools** — practical scripts (`flagfinder.py`, `quick_scan.sh`, `decode_helpers.py`)
- **/labs** — lab sandboxes and PoCs (annotated)
- **/docs/screenshots** — demo GIFs & screenshots (animated)
- **/docker** — `Dockerfile` and container helpers
- **.github/workflows** — CI & build pipelines

---

## ✦ Why animations?
Animated demos (GIFs or asciinema recordings) quickly show tool outputs and make your repo stand out in portfolios. Place your demo GIF at `/docs/screenshots/demo.gif`.

---

## 🚀 Install & Full Setup (container + venv)
### Option A — Run quickly using Docker (recommended)
```bash
# build once
docker build -t pico-hacker:latest .

# run interactive sandbox
docker run --rm -it -v "$(pwd)":/workspace pico-hacker:latest /bin/bash
