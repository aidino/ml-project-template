<div align="center">
<h1><a href="">Project name</a></h1>
<a target="_blank" href="https://pytorch.org/"><img src="https://img.shields.io/badge/pytorch-d35400"></a>&nbsp;
<a target="_blank" href="https://www.ray.io/"><img src="https://img.shields.io/badge/ray-2980b9"></a>&nbsp;
<a target="_blank" href=""><img src="https://img.shields.io/badge/nlp-white"></a>&nbsp;
<a target="_blank" href=""><img src="https://img.shields.io/badge/classification-white"></a>&nbsp;
</div>


## Overview

Project description

## Virtual environment

- Download and switch between Python versions.

```bash 
pyenv install 3.10.11  # install
pyenv global 3.10.11  # set default
```

- Create a virtual environment.

```bash
export PYTHONPATH=$PYTHONPATH:$PWD
python3 -m venv venv  # recommend using Python 3.10
source venv/bin/activate  # on Windows: venv\Scripts\activate
python3 -m pip install --upgrade pip setuptools wheel
python3 -m pip install -r requirements.txt
pre-commit install
pre-commit autoupdate
```

## Notebook

<div align="center">
  <img src="https://madewithml.com/static/images/mlops/systems-design/workloads.png">
</div>

Source: [Made With ML](https://madewithml.com/)

## Script

```bash
src
├── config.py
├── data.py
├── evaluate.py
├── models.py
├── predict.py
├── serve.py
├── train.py
├── tune.py
└── utils.py
```

## Pre-commit

- Run

```bash
# Run
pre-commit run --all-files  # run all hooks on all files
pre-commit run <HOOK_ID> --all-files # run one hook on all files
pre-commit run --files <PATH_TO_FILE>  # run all hooks on a file
pre-commit run <HOOK_ID> --files <PATH_TO_FILE> # run one hook on a file
```

- Skip

```bash
# Commit without hooks
git commit -m <MESSAGE> --no-verify
```

- Update

```bash
# Autoupdate
pre-commit autoupdate
```
