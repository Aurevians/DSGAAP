# Anaconda Installation & Configuration Guide for macOS

## Homebrew • Conda • Python 3.13.11 • pyenv • JupyterLab • Jupyter Notebook

A practical, end-to-end guide for installing and configuring **Anaconda on macOS** while maintaining a clean Python development environment using **Conda**, **pyenv**, and **Jupyter**.

This guide includes solutions for common real-world issues:

- Conda SSL certificate failures behind corporate proxies (for example, Zscaler)
- Conda permission issues
- Managing multiple Python installations
- Preventing Anaconda Base from overriding the default Python
- Configuring Jupyter kernels correctly

---

# 📖 Table of Contents

- [Overview](#overview)
- [What This Setup Provides](#what-this-setup-provides)
- [Environment Architecture](#environment-architecture)
- [Prerequisites](#prerequisites)
- [Installation Guide](#installation-guide)
  - [Step 1 - Install Anaconda](#step-1---install-anaconda)
  - [Step 2 - Initialize Conda](#step-2---initialize-conda)
  - [Step 3 - Disable Conda Base Auto Activation](#step-3---disable-conda-base-auto-activation)
  - [Step 4 - Configure pyenv Python](#step-4---configure-pyenv-python)
  - [Step 5 - Create Python 3.13.11 Conda Environment](#step-5---create-python-31311-conda-environment)
  - [Step 6 - Fix Conda Permissions](#step-6---fix-conda-permissions)
- [Continue Setup](#continue-setup)

---

# Overview

This guide installs Anaconda on macOS using Homebrew and configures it for Python development.

The final setup provides:

- Anaconda installed through Homebrew
- Conda environment management
- Python 3.13.11 development environment
- pyenv-managed default Python
- JupyterLab support
- Classic Jupyter Notebook support
- Clean separation between:
  - macOS system Python
  - pyenv Python
  - Conda Python environments

---

# What This Setup Provides

After completing this guide:

| Component | Purpose |
|---|---|
| Homebrew | macOS package manager |
| Anaconda | Python distribution and Conda package manager |
| Conda | Environment and package management |
| pyenv | Default Python version management |
| Python 3.13.11 | Development Python version |
| JupyterLab | Modern notebook interface |
| Jupyter Notebook | Classic notebook interface |

---

# Environment Architecture

```text
                         Terminal
                            |
                            |
                    pyenv managed Python
                            |
                     Python 3.13.11
                            |
          +-----------------+----------------+
          |                                  |
          |                                  |
 Normal Python Development          Conda Development
                                           |
                                   conda activate py313
                                           |
                                  Python 3.13.11
                                           |
                         +-----------------+----------------+
                         |                 |                |
                    JupyterLab       Jupyter Notebook    Packages


Anaconda Base
    |
    +-- Installed
    +-- Not automatically activated


macOS System Python
    |
    +-- Unchanged
```

---

# Prerequisites

Verify Homebrew and pyenv are installed:

```bash
brew --version
```

```bash
pyenv --version
```

If Homebrew is not installed:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

# Installation Guide

## Step 1 - Install Anaconda

Install Anaconda using Homebrew:

```bash
brew install --cask anaconda
```

The installation location will typically be:

```text
/opt/homebrew/anaconda3
```

Verify:

```bash
ls /opt/homebrew/anaconda3
```

Expected directories:

```text
bin
conda-meta
envs
pkgs
```

---

# Step 2 - Initialize Conda

Initialize Conda for the Zsh shell:

```bash
/opt/homebrew/anaconda3/bin/conda init zsh
```

Reload your shell:

```bash
source ~/.zshrc
```

Verify Conda:

```bash
conda --version
```

Example:

```text
conda 26.x.x
```

---

# Step 3 - Disable Conda Base Auto Activation

By default, Anaconda activates the `base` environment whenever a terminal starts.

Disable this behavior:

```bash
conda config --set auto_activate_base false
```

Verify:

```bash
conda config --show auto_activate_base
```

Expected:

```text
auto_activate_base: false
```

Restart Terminal.

Your prompt should no longer automatically show:

```text
(base)
```

---

# Step 4 - Configure pyenv Python

## Install Python 3.13.11

```bash
pyenv install 3.13.11
```

Example output:

```text
Installed Python-3.13.11 to ~/.pyenv/versions/3.13.11
```

---

## Set Python 3.13.11 as Default

```bash
pyenv global 3.13.11
```

Reload shell:

```bash
exec zsh
```

Verify:

```bash
python --version
```

Expected:

```text
Python 3.13.11
```

Check location:

```bash
which python
```

Expected:

```text
/Users/<username>/.pyenv/shims/python
```

---

# Why pyenv is Used

macOS includes a system Python installation.

Homebrew may also install Python versions.

Without version management, commands such as:

```bash
python
python3
pip
```

can point to different installations.

pyenv ensures:

```text
python
    |
    +-- ~/.pyenv/shims/python
            |
            +-- Python 3.13.11
```

Your macOS Python remains untouched.

---

# Step 5 - Create Python 3.13.11 Conda Environment

Create a dedicated Conda environment:

```bash
conda create -n py313 python=3.13.11
```

Activate:

```bash
conda activate py313
```

Verify:

```bash
python --version
```

Expected:

```text
Python 3.13.11
```

Check location:

```bash
which python
```

Expected:

```text
/opt/homebrew/anaconda3/envs/py313/bin/python
```

---

# Step 6 - Fix Conda Permissions

If you see errors like:

```text
Unable to create environments file
Path not writable
```

Check ownership:

```bash
ls -ld ~/.conda
```

If owned by `root`, fix permissions:

```bash
sudo chown -R $(whoami):staff ~/.conda
```

Apply permissions:

```bash
chmod -R u+rwX ~/.conda
```

Verify:

```bash
ls -ld ~/.conda
```

Expected:

```text
<your-user> staff
```

---

# Continue Setup

Continue with:

- Installing JupyterLab
- Installing Jupyter Notebook
- Registering kernels
- Verification
- Troubleshooting

# Continue Setup

---

# Step 7 - Install JupyterLab

Activate the Conda environment:

```bash
conda activate py313
```

Install JupyterLab and kernel support:

```bash
conda install jupyterlab ipykernel
```

Accept the Anaconda Terms of Service when prompted.

Verify:

```bash
jupyter lab --version
```

---

# Step 8 - Register the Jupyter Kernel

Register the Conda environment as a Jupyter kernel:

```bash
python -m ipykernel install \
  --user \
  --name py313 \
  --display-name "Python 3.13.11 (py313)"
```

Expected output:

```text
Installed kernelspec py313
```

Verify:

```bash
jupyter kernelspec list
```

Example:

```text
Available kernels:

python3    /opt/homebrew/anaconda3/envs/py313/share/jupyter/kernels/python3
py313      /Users/<username>/Library/Jupyter/kernels/py313
```

---

# Step 9 - Install Classic Jupyter Notebook

JupyterLab already supports notebooks, but if you prefer the classic Notebook interface, install it:

```bash
conda activate py313
conda install notebook
```

Verify:

```bash
jupyter notebook --version
```

---

# Using Jupyter

## Launch JupyterLab

Activate the environment:

```bash
conda activate py313
```

Start JupyterLab:

```bash
jupyter lab
```

The browser should open automatically:

```text
http://localhost:8888/lab
```

If it does not open, copy the URL displayed in the terminal.

---

## Launch Classic Jupyter Notebook

Start:

```bash
conda activate py313
jupyter notebook
```

Browser URL:

```text
http://localhost:8888/tree
```

---

# Verify the Jupyter Python Kernel

Create a new notebook.

Select:

```text
Python 3.13.11 (py313)
```

Run:

```python
import sys

print(sys.version)
print(sys.executable)
```

Expected:

```text
3.13.11 ...
/opt/homebrew/anaconda3/envs/py313/bin/python
```

This confirms that Jupyter is using the correct Conda environment.

---

# Stop Jupyter

Return to the terminal running Jupyter.

Press:

```text
Ctrl + C
```

Confirm:

```text
y
```

---

# Daily Workflow

## Normal Python Development

No Conda environment active:

```bash
python --version
```

Expected:

```text
Python 3.13.11
```

Python location:

```bash
which python
```

Expected:

```text
/Users/<username>/.pyenv/shims/python
```

---

## Conda Development

Activate the environment:

```bash
conda activate py313
```

Python now comes from:

```text
/opt/homebrew/anaconda3/envs/py313/bin/python
```

---

## Start JupyterLab

```bash
jupyter lab
```

---

## Start Jupyter Notebook

```bash
jupyter notebook
```

---

## Leave Conda

When finished:

```bash
conda deactivate
```

Terminal returns to:

```text
username@Mac ~ %
```

---

# Verification Checklist

Use this checklist after installation.

## Anaconda

- [ ] Anaconda installed successfully
- [ ] Conda command available

Verify:

```bash
conda --version
```

---

## Conda Configuration

- [ ] Base auto activation disabled

Verify:

```bash
conda config --show auto_activate_base
```

Expected:

```text
auto_activate_base: false
```

---

## Python

Outside Conda:

```bash
python --version
```

Expected:

```text
Python 3.13.11
```

Inside Conda:

```bash
conda activate py313

python --version
```

Expected:

```text
Python 3.13.11
```

---

## Conda Environment

Verify:

```bash
conda env list
```

Expected:

```text
base
py313 *
```

---

## Jupyter

Verify kernels:

```bash
jupyter kernelspec list
```

Expected:

```text
py313
```

---

# Useful Conda Commands

## List Environments

```bash
conda env list
```

---

## Activate Environment

```bash
conda activate py313
```

---

## Deactivate Environment

```bash
conda deactivate
```

---

## Install Package

Using Conda:

```bash
conda install package-name
```

Using pip:

```bash
pip install package-name
```

---

## Update Environment

```bash
conda update --all
```

---

## Export Environment

Backup environment:

```bash
conda env export > py313-environment.yml
```

---

## Recreate Environment

```bash
conda env create -f py313-environment.yml
```

---

# Troubleshooting

---

# Issue 1 - Conda SSL Certificate Error

Example:

```text
CondaSSLError:
certificate verify failed
```

Common causes:

- Corporate proxy
- TLS inspection tools
- Missing company CA certificate

Example:

```text
Zscaler SSL inspection
```

Temporary workaround:

```bash
conda config --set ssl_verify false
```

Verify:

```bash
conda config --show ssl_verify
```

Expected:

```text
ssl_verify: False
```

After the correct corporate certificate is installed, restore:

```bash
conda config --set ssl_verify true
```

---

# Issue 2 - Conda Permission Error

Example:

```text
Unable to create environments file
Path not writable
```

Fix:

```bash
sudo chown -R $(whoami):staff ~/.conda
chmod -R u+rwX ~/.conda
```

---

# Issue 3 - Wrong Python Version Appears

Check:

```bash
which python
python --version
```

If outside Conda:

Expected:

```text
/Users/<username>/.pyenv/shims/python
Python 3.13.11
```

If inside Conda:

Expected:

```text
/opt/homebrew/anaconda3/envs/py313/bin/python
Python 3.13.11
```

---

# Issue 4 - Jupyter Command Not Found

Activate Conda:

```bash
conda activate py313
```

Verify:

```bash
which jupyter
```

If missing:

```bash
conda install jupyterlab notebook ipykernel
```

---

# Final Environment Layout

```text
macOS
│
├── System Python
│       └── Unchanged
│
├── pyenv
│       └── Python 3.13.11
│              └── Default terminal Python
│
└── Anaconda
        │
        ├── base
        │      └── Installed
        │      └── Not auto activated
        │
        └── py313
               ├── Python 3.13.11
               ├── JupyterLab
               ├── Jupyter Notebook
               └── Development Packages
```

---

# 🎉 Setup Complete

Your macOS Python environment is now configured with:

✅ Anaconda installed through Homebrew  
✅ Conda environment management  
✅ Python 3.13.11 development environment  
✅ pyenv-managed default Python  
✅ JupyterLab support  
✅ Classic Jupyter Notebook support  
✅ Correct Jupyter kernel configuration  
✅ Separation between system Python, pyenv, and Conda  

This setup provides a clean, reproducible Python development environment suitable for data science, automation, machine learning, and general Python development.
