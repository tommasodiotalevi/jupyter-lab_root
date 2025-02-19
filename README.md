# JupyterLab with pyROOT Jupyter kernel
[![jupyterlab_image](https://github.com/tommasodiotalevi/jupyter-lab_root/actions/workflows/docker-image.yml/badge.svg)](https://github.com/tommasodiotalevi/jupyter-lab_root/actions/workflows/docker-image.yml)

Repository for creating a JupyterLab image with pyROOT 6.28.

## How to run it

- Make sure you have [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed on your computer;
- Clone this repository;
- Inside the repository, use the command `docker compose up` to launch the Jupyterlab service;
- In your computer browser, type `127.0.0.1:8888` and use `docker` as password to enter.

Inside the repository, you will find a folder named `persistent-storage`: put here all the code or external repositories you want to use in the Jupyter environment. Everything outside of this folder will not be visible inside the container.