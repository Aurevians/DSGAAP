# Anaconda + Python 3.13 + Jupyter Notebook Setup Runbook

## Apple Silicon macOS

This runbook provides an end-to-end procedure for installing and
configuring Anaconda on Apple Silicon Macs and preparing a Python
3.13.11 development environment with Jupyter Notebook and JupyterLab.

It is intended for team members who need a reproducible development
setup, including corporate environments using SSL inspection such as
Zscaler.

> **Scope:** Apple Silicon (`arm64`) Macs using Zsh and Homebrew.
>
> **Security:** Do not leave Conda SSL verification disabled as a
> permanent solution. Corporate CA/Zscaler issues should be resolved
> using an IT-approved CA configuration.

------------------------------------------------------------------------

## Table of Contents

1.  [Target Configuration](#1-target-configuration)
2.  [Prerequisites](#2-prerequisites)
3.  [Install Anaconda](#3-install-anaconda)
4.  [Initialize Conda](#4-initialize-conda)
5.  [Disable Automatic Base
    Activation](#5-disable-automatic-base-activation)
6.  [Check Existing Python
    Configuration](#6-check-existing-python-configuration)
7.  [Install Python 3.13.11 with
    pyenv](#7-install-python-31311-with-pyenv)
8.  [Verify PATH Ordering](#8-verify-path-ordering)
9.  [Create the Conda Python 3.13
    Environment](#9-create-the-conda-python-313-environment)
10. [Verify pip Isolation](#10-verify-pip-isolation)
11. [Fix `.conda` Permissions](#11-fix-conda-permissions)
12. [Install JupyterLab and IPython
    Kernel](#12-install-jupyterlab-and-ipython-kernel)
13. [Register `py313` as a Jupyter
    Kernel](#13-register-py313-as-a-jupyter-kernel)
14. [Install Jupyter Notebook](#14-install-jupyter-notebook)
15. [Start Jupyter Notebook](#15-start-jupyter-notebook)
16. [Start JupyterLab](#16-start-jupyterlab)
17. [Select and Verify the Correct
    Kernel](#17-select-and-verify-the-correct-kernel)
18. [Stop Jupyter Correctly](#18-stop-jupyter-correctly)
19. [Final Validation Checklist](#19-final-validation-checklist)
20. [Troubleshooting Guide](#20-troubleshooting-guide)
21. [Quick Reference](#21-quick-reference)
22. [Team Operational Guidelines](#22-team-operational-guidelines)
23. [Standard Daily Workflow — Jupyter Notebook](#23-standard-daily-workflow--jupyter-notebook)
24. [Standard Daily Workflow — JupyterLab](#24-standard-daily-workflow--jupyterlab)

------------------------------------------------------------------------

## 1. Target Configuration

The final configuration should look like this:

``` text
Apple Silicon macOS
|
+-- Homebrew
|
+-- pyenv
|   +-- Default terminal Python 3.13.11
|
+-- Anaconda
    +-- /opt/homebrew/anaconda3
        |
        +-- base
        |   +-- Auto-activation disabled
        |
        +-- envs
            +-- py313
                +-- Python 3.13.11
                +-- pip
                +-- JupyterLab
                +-- Jupyter Notebook
                +-- IPython kernel
```

The normal terminal Python and the Anaconda development environment are
intentionally kept separate.

------------------------------------------------------------------------

## 2. Prerequisites

Confirm the Mac is Apple Silicon:

``` bash
uname -m
```

Expected:

``` text
arm64
```

Check Homebrew:

``` bash
brew --version
brew --prefix
```

Expected Homebrew prefix:

``` text
/opt/homebrew
```

Update Homebrew:

``` bash
brew update
```

This runbook assumes Zsh, which is the default shell on current macOS
versions.

------------------------------------------------------------------------

## 3. Install Anaconda

Install Anaconda:

``` bash
brew install --cask anaconda
```

On Apple Silicon, Homebrew installs Anaconda under:

``` text
/opt/homebrew/anaconda3
```

During installation, output may include:

``` text
PREFIX=/opt/homebrew/anaconda3
```

Do not assume the installation is under `~/anaconda3`.

Verify the executable:

``` bash
ls -l /opt/homebrew/anaconda3/bin/conda
```

------------------------------------------------------------------------

## 4. Initialize Conda

Initialize Conda for Zsh:

``` bash
/opt/homebrew/anaconda3/bin/conda init zsh
```

Reload the shell configuration:

``` bash
source ~/.zshrc
```

The prompt may temporarily show `(base)`.

Verify:

``` bash
conda --version
type conda
```

It is normal for `type conda` to report that Conda is a shell function
loaded from `~/.zshrc`. This shell integration is required for
`conda activate` and `conda deactivate`.

------------------------------------------------------------------------

## 5. Disable Automatic Base Activation

Prevent Anaconda `base` from taking over every new terminal:

``` bash
conda config --set auto_activate_base false
```

Verify:

``` bash
conda config --show auto_activate_base
```

Expected:

``` text
auto_activate_base: false
```

If `base` is currently active:

``` bash
conda deactivate
```

Restart the shell:

``` bash
exec zsh
```

The `(base)` prefix should disappear, while `conda --version` should
continue to work.

------------------------------------------------------------------------

## 6. Check Existing Python Configuration

Before changing Python, determine which installation currently controls
the shell:

``` bash
which python
python --version
which python3
python3 --version
type -a python
type -a python3
```

If the path contains:

``` text
~/.pyenv/shims/python
```

then `pyenv` controls the default Python.

Check:

``` bash
pyenv version
```

------------------------------------------------------------------------

## 7. Install Python 3.13.11 with pyenv

If Python 3.13.11 is the team standard:

``` bash
pyenv install 3.13.11
pyenv global 3.13.11
exec zsh
```

Verify:

``` bash
which python
python --version
which python3
python3 --version
```

Expected:

``` text
~/.pyenv/shims/python
Python 3.13.11
~/.pyenv/shims/python3
Python 3.13.11
```

If `pyenv install` reports build dependency problems, ensure the
required Homebrew build libraries are installed according to your team's
standard workstation configuration.

------------------------------------------------------------------------

## 8. Verify PATH Ordering

Inspect:

``` bash
echo $PATH
```

The Conda initialization may place this on PATH:

``` text
/opt/homebrew/anaconda3/condabin
```

That is appropriate because it exposes the `conda` command without
making Anaconda's base Python the normal shell Python.

Avoid permanently placing this ahead of the normal Python setup:

``` text
/opt/homebrew/anaconda3/bin
```

Verify priority:

``` bash
type -a python
type -a python3
```

Outside a Conda environment, the preferred Python should be the `pyenv`
shim.

------------------------------------------------------------------------

## 9. Create the Conda Python 3.13 Environment

Create the dedicated environment:

``` bash
conda create -n py313 python=3.13.11
```

Activate it:

``` bash
conda activate py313
```

Verify:

``` bash
python --version
which python
```

Expected:

``` text
Python 3.13.11
/opt/homebrew/anaconda3/envs/py313/bin/python
```

Check environments:

``` bash
conda env list
```

Expected structure:

``` text
base      /opt/homebrew/anaconda3
py313   * /opt/homebrew/anaconda3/envs/py313
```

------------------------------------------------------------------------

## 10. Verify pip Isolation

While `py313` is active:

``` bash
which pip
pip --version
python -m pip --version
```

The path should be under:

``` text
/opt/homebrew/anaconda3/envs/py313/
```

This confirms package installations are isolated from macOS Python,
Homebrew Python, pyenv Python, and Anaconda `base`.

------------------------------------------------------------------------

## 11. Fix `.conda` Permissions

If Conda cannot write user metadata, inspect:

``` bash
ls -ld ~/.conda
```

If `~/.conda` is owned by `root`, correct it:

``` bash
sudo chown -R "$(whoami)":staff ~/.conda
chmod -R u+rwX ~/.conda
```

Verify:

``` bash
ls -ld ~/.conda
```

The current user should own the directory.

> Do not use `sudo conda install`.

------------------------------------------------------------------------

## 12. Install JupyterLab and IPython Kernel

Activate the environment:

``` bash
conda activate py313
```

Install:

``` bash
conda install jupyterlab ipykernel
```

If Anaconda requests Terms of Service acceptance, review and accept
according to organizational policy.

Verify:

``` bash
jupyter --version
```

------------------------------------------------------------------------

## 13. Register `py313` as a Jupyter Kernel

While `py313` is active:

``` bash
python -m ipykernel install --user \
  --name py313 \
  --display-name "Python 3.13.11 (py313)"
```

Verify:

``` bash
jupyter kernelspec list
```

The output should include a `py313` kernelspec, typically under:

``` text
~/Library/Jupyter/kernels/py313
```

------------------------------------------------------------------------

## 14. Install Jupyter Notebook

JupyterLab does not necessarily install the `jupyter notebook` command.

If this fails:

``` bash
jupyter notebook
```

with:

``` text
Jupyter command `jupyter-notebook` not found.
```

install Notebook:

``` bash
conda install notebook
```

Verify:

``` bash
jupyter notebook --version
```

In the setup used to develop this runbook, the installed Notebook
version was `7.5.7`. The current version may differ.

------------------------------------------------------------------------

## 15. Start Jupyter Notebook

Navigate to the project directory first:

``` bash
cd <project-directory>
```

Activate the environment:

``` bash
conda activate py313
```

Start Notebook:

``` bash
jupyter notebook
```

Jupyter will use the current project directory as its starting location
and should open in the default browser.

Recommended daily sequence:

``` bash
cd <project-directory>
conda activate py313
jupyter notebook
```

------------------------------------------------------------------------

## 16. Start JupyterLab

For JupyterLab instead:

``` bash
cd <project-directory>
conda activate py313
jupyter lab
```

Use Notebook or Lab according to project/team preference; both should
use the same `py313` environment.

------------------------------------------------------------------------

## 17. Select and Verify the Correct Kernel

When opening or creating a notebook, select:

``` text
Python 3.13.11 (py313)
```

Run this validation cell:

``` python
import sys

print(sys.version)
print(sys.executable)
```

Expected:

``` text
3.13.11 ...
/opt/homebrew/anaconda3/envs/py313/bin/python
```

This check is important when diagnosing package-import or Python-version
problems.

------------------------------------------------------------------------

## 18. Stop Jupyter Correctly

Save notebook work, return to the terminal that launched Jupyter, and
press:

``` text
Ctrl+C
```

If prompted:

``` text
Shut down this Jupyter server (y/[n])?
```

enter:

``` text
y
```

Then deactivate the environment:

``` bash
conda deactivate
```

To verify no server remains:

``` bash
conda activate py313
jupyter server list
```

A fully stopped setup shows:

``` text
Currently running servers:
```

with nothing underneath.

### Avoid `conda run` for interactive Jupyter sessions

Although this works:

``` bash
conda run -n py313 jupyter lab
```

interrupting it with `Ctrl+C` can surface:

``` text
CondaError: KeyboardInterrupt
```

For interactive use, prefer explicit activation:

``` bash
conda activate py313
jupyter notebook
```

or:

``` bash
conda activate py313
jupyter lab
```

------------------------------------------------------------------------

## 19. Final Validation Checklist

### Outside Conda

Ensure the environment is deactivated:

``` bash
conda deactivate
```

Check:

``` bash
python --version
which python
conda --version
```

Expected Python:

``` text
Python 3.13.11
~/.pyenv/shims/python
```

Conda should remain available.

### Inside Conda

``` bash
conda activate py313
python --version
which python
which pip
conda env list
jupyter notebook --version
jupyter kernelspec list
```

Expected key paths:

``` text
/opt/homebrew/anaconda3/envs/py313/bin/python
/opt/homebrew/anaconda3/envs/py313/bin/pip
```

The `py313` kernel should be listed.

### Notebook validation

Inside a notebook using `Python 3.13.11 (py313)`:

``` python
import sys
print(sys.version)
print(sys.executable)
```

Confirm Python 3.13.11 and the `py313` executable path.

------------------------------------------------------------------------

## 20. Troubleshooting Guide

### 20.1 `~/anaconda3/bin/conda: no such file or directory`

**Cause:** Homebrew installed Anaconda under the Apple Silicon Homebrew
prefix.

Use:

``` bash
/opt/homebrew/anaconda3/bin/conda
```

Verify:

``` bash
ls -l /opt/homebrew/anaconda3/bin/conda
```

------------------------------------------------------------------------

### 20.2 `(base)` Appears Every Time Terminal Opens

Run:

``` bash
conda config --set auto_activate_base false
conda deactivate
exec zsh
```

Verify:

``` bash
conda config --show auto_activate_base
```

------------------------------------------------------------------------

### 20.3 Wrong Python Version Outside Conda

Check:

``` bash
which python
python --version
pyenv version
type -a python
```

If the team uses `pyenv`, set:

``` bash
pyenv global 3.13.11
exec zsh
```

The preferred path is:

``` text
~/.pyenv/shims/python
```

------------------------------------------------------------------------

### 20.4 Conda SSL Certificate Error

Typical errors include:

``` text
CondaSSLError
CERTIFICATE_VERIFY_FAILED
unable to get local issuer certificate
```

Test system HTTPS access:

``` bash
curl -I https://repo.anaconda.com
```

Check proxy/certificate variables:

``` bash
env | grep -i -E "proxy|ssl|cert"
```

On a corporate workstation, this may reveal SSL inspection such as
Zscaler.

The correct long-term solution is to obtain and configure the
organization's approved CA certificate for Conda/OpenSSL.

#### Temporary diagnostic workaround only

``` bash
conda config --set ssl_verify false
```

After testing, restore secure verification:

``` bash
conda config --set ssl_verify true
```

> **Do not leave `ssl_verify: False` as the permanent team
> configuration.**

------------------------------------------------------------------------

### 20.5 Zscaler Certificate: `Basic Constraints of CA cert not marked critical`

During the setup used to create this runbook, an existing Zscaler
certificate was detected, but newer OpenSSL validation rejected it with:

``` text
Basic Constraints of CA cert not marked critical
```

Do not modify the corporate certificate yourself.

Request an IT/security-approved CA certificate that is compatible with
the organization's Conda/OpenSSL configuration.

------------------------------------------------------------------------

### 20.6 Conda Cannot Write `~/.conda`

Check:

``` bash
ls -ld ~/.conda
```

If root-owned:

``` bash
sudo chown -R "$(whoami)":staff ~/.conda
chmod -R u+rwX ~/.conda
```

------------------------------------------------------------------------

### 20.7 `jupyter notebook` Command Not Found

If:

``` bash
jupyter notebook
```

reports:

``` text
Jupyter command `jupyter-notebook` not found.
```

install:

``` bash
conda activate py313
conda install notebook
```

Then verify:

``` bash
jupyter notebook --version
```

------------------------------------------------------------------------

### 20.8 `jupyter` Not Found Outside Conda

This is expected if Jupyter was installed only in `py313`.

Use:

``` bash
conda activate py313
jupyter notebook
```

Keeping Jupyter inside the project environment avoids unnecessary global
package overlap.

------------------------------------------------------------------------

### 20.9 Notebook Uses the Wrong Python

Inside the notebook:

``` python
import sys
print(sys.executable)
```

Expected:

``` text
/opt/homebrew/anaconda3/envs/py313/bin/python
```

If it differs, select:

``` text
Python 3.13.11 (py313)
```

from the notebook kernel selector.

------------------------------------------------------------------------

### 20.10 Check for a Leftover Jupyter Server

``` bash
conda activate py313
jupyter server list
```

If no server is running, nothing will appear below:

``` text
Currently running servers:
```

------------------------------------------------------------------------

## 21. Quick Reference

### Start Jupyter Notebook

``` bash
cd <project-directory>
conda activate py313
jupyter notebook
```

### Start JupyterLab

``` bash
cd <project-directory>
conda activate py313
jupyter lab
```

### Stop Jupyter

Press:

``` text
Ctrl+C
```

Confirm shutdown with `y` if prompted, then:

``` bash
conda deactivate
```

### Check Python

``` bash
python --version
which python
```

### Check Conda Environments

``` bash
conda env list
```

### Check Jupyter Kernels

``` bash
jupyter kernelspec list
```

### Check Running Jupyter Servers

``` bash
jupyter server list
```

------------------------------------------------------------------------

## 22. Team Operational Guidelines

1.  Do not use `sudo conda install`.
2.  Keep Anaconda `base` auto-activation disabled.
3.  Use dedicated Conda environments instead of installing project
    packages into `base`.
4.  Check `which python` and `python --version` before troubleshooting
    version problems.
5.  Check `sys.executable` inside notebooks when troubleshooting kernel
    or dependency problems.
6.  Do not permanently disable Conda SSL verification to bypass
    corporate SSL inspection.
7.  Resolve Zscaler/corporate CA issues through the organization's
    approved IT/security process.
8.  Do not modify Apple's system-managed Python.
9.  Install project dependencies into the appropriate project
    environment.
10. Start Jupyter from the intended project directory.
11. Use the explicitly registered `Python 3.13.11 (py313)` kernel when
    consistency matters.
12. Stop the Jupyter server before closing the development session.

------------------------------------------------------------------------

## 23. Standard Daily Workflow — Jupyter Notebook

```bash
cd <project-directory>
conda activate py313
jupyter notebook
```

When finished:

1. Save your notebook work.
2. Return to the terminal running Jupyter Notebook.
3. Press `Ctrl+C`.
4. Confirm shutdown with `y` if prompted.
5. Deactivate the Conda environment:

```bash
conda deactivate
```

---

## 24. Standard Daily Workflow — JupyterLab

```bash
cd <project-directory>
conda activate py313
jupyter lab
```

When finished:

1. Save your work.
2. Return to the terminal running JupyterLab.
3. Press `Ctrl+C`.
4. Confirm shutdown with `y` if prompted.
5. Deactivate the Conda environment:

```bash
conda deactivate
```

Both workflows use the same `py313` Conda environment and the registered `Python 3.13.11 (py313)` kernel.
