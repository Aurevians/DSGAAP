# Developer Environment Setup Guides

This directory contains practical installation and configuration guides for developer tools and environments used across DSGAAP.

These guides focus on:

- Reproducible developer setups
- Clean environment configuration
- Step-by-step installation procedures
- Environment validation
- Troubleshooting common installation issues
- Useful commands and development best practices

---

## 📚 Documentation Index

### Python & Anaconda

| Guide | Description | Platform |
| --- | --- | --- |
| [Anaconda + Jupyter Setup Guide](./anaconda-jupyter-setup.md) | Complete Anaconda installation and configuration guide including Conda, Python 3.13.11, pyenv, JupyterLab, Jupyter Notebook, kernel configuration, validation, and troubleshooting. | Apple Silicon macOS |

Additional setup guides will be added as new development tools and technologies are introduced.

---

## 🚀 Quick Start

If Anaconda and Jupyter are already installed and configured, use the appropriate daily workflow.

### Jupyter Notebook

➡️ [Jupyter Notebook Daily Workflow](./anaconda-jupyter-setup.md#23-standard-daily-workflow--jupyter-notebook)

### JupyterLab

➡️ [JupyterLab Daily Workflow](./anaconda-jupyter-setup.md#24-standard-daily-workflow--jupyterlab)

For first-time installation, configuration, validation, or troubleshooting:

➡️ [Complete Anaconda + Jupyter Setup Guide](./anaconda-jupyter-setup.md)

---

## 🛠️ Covered Topics

### Python Development

- Anaconda installation using Homebrew
- Conda initialization and configuration
- Conda environment management
- Python version management using `pyenv`
- Python 3.13.11 environment setup
- Python and Conda environment isolation
- JupyterLab installation and configuration
- Jupyter Notebook installation and configuration
- Jupyter kernel management
- Development environment validation

### Troubleshooting

- SSL certificate errors
- Corporate proxy and SSL inspection issues such as Zscaler
- Conda permission issues
- Python version conflicts
- PATH configuration issues
- Conda environment issues
- Jupyter kernel issues
- Jupyter server troubleshooting

---

## 📂 Directory Structure

```text
DSGAAP/
│
└── Installation-Docs/
    ├── README.md
    └── anaconda-jupyter-setup.md
```

As additional installation guides are created:

```text
Installation-Docs/
├── README.md
├── anaconda-jupyter-setup.md
├── docker-setup.md
├── nodejs-setup.md
├── vscode-setup.md
└── ...
```

This `README.md` serves as the central index for DSGAAP installation documentation.

---

## 📝 Documentation Convention

Use descriptive lowercase filenames with words separated by hyphens:

```text
<technology>-setup.md
```

Examples:

```text
anaconda-jupyter-setup.md
docker-setup.md
nodejs-setup.md
vscode-setup.md
```

Where applicable, each installation guide should contain:

- Purpose and target configuration
- Prerequisites
- Installation instructions
- Environment configuration
- PATH or shell configuration
- Version verification
- Security or corporate-network considerations
- Troubleshooting guidance
- Final validation checklist
- Quick reference commands
- Standard daily workflows

---

## ➕ Adding a New Installation Guide

When adding a new guide:

1. Create the `<technology>-setup.md` file in this directory.
2. Add it to the **Documentation Index** above.
3. Add a **Quick Start** link when the guide contains a repeatable daily workflow.
4. Verify all relative Markdown links.
5. Confirm commands and validation steps on a supported environment where practical.
6. Document relevant platform-specific or corporate-network considerations.

---

## 🔄 Maintaining the Documentation

When an installation procedure changes:

1. Update the corresponding installation guide.
2. Update version-specific commands or expected output.
3. Add newly discovered troubleshooting scenarios.
4. Update Quick Start links if workflow headings change.
5. Update this README whenever a new installation guide is added.

---

## 🎯 Goal

The goal is to make developer environment setup:

**Reproducible · Consistent · Maintainable · Easy to Troubleshoot**

Return to the [DSGAAP repository home](../README.md).
