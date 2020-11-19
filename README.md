# Examples of using Cython and nvc++ to GPU accelerate Python

See the accompanying post on the NVIDIA Developer Blog [here](https://developer.nvidia.com/blog/accelerating-python-on-gpus-with-nvc-and-cython/).

These Notebooks demonstrate how to accelerate Python code on the GPU
using Cython and [nvc++ with stdpar](https://developer.nvidia.com/blog/accelerating-standard-c-with-gpus-using-stdpar/).

1. [Simple sort Notebook](sort.ipynb)
2. [Jacobi solver Notebook](jacobi.ipynb)

## PB NOTES

### Scaricare HPC-SDK
Fare riferimento al sito [Nvidia](https://developer.nvidia.com/nvidia-hpc-sdk-downloads).

`nvidia-smi` produce questo

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
```

Dunque andiamo con l'ultima versione. Entrambi i files sono salvati dentro
`/media/xnext/DATAEXT/nvidia/hpc-sdk/`

```
wget \
  https://developer.download.nvidia.com/hpc-sdk/20.9/nvhpc-20-9_20.9_amd64.deb \
  https://developer.download.nvidia.com/hpc-sdk/20.9/nvhpc-2020_20.9_amd64.deb
sudo apt-get install ./nvhpc-20-9_20.9_amd64.deb ./nvhpc-2020_20.9_amd64.deb
```

Dopodiche' la [documentazione](https://docs.nvidia.com/hpc-sdk/hpc-sdk-install-guide/index.html) suggerisce
di aggiungere a `~./bashrc` le seguenti

```
NVARCH=`uname -s`_`uname -m`; export NVARCH
NVCOMPILERS=/opt/nvidia/hpc_sdk; export NVCOMPILERS
MANPATH=$MANPATH:$NVCOMPILERS/$NVARCH/20.9/compilers/man; export MANPATH
PATH=$NVCOMPILERS/$NVARCH/20.9/compilers/bin:$PATH; export PATH
# opzionali
export PATH=$NVCOMPILERS/$NVARCH/20.9/comm_libs/mpi/bin:$PATH
export MANPATH=$MANPATH:$NVCOMPILERS/$NVARCH/20.9/comm_libs/mpi/man
```

You can run them inside a Docker container with
([more on that](https://max6log.wordpress.com/2017/11/05/easy-jupyter-notebook-with-docker/))

```
# cd to this repo first
docker run -it --rm
    -v /media/xnext/DATAEXT/git_repos/gpustdparpy/:/home/jovyan/work/notebooks \
    -p 8888:8888 jupyter/datascience-notebook
```

But what about external dependencies (such as TBB!!!).
It's better to install Jupyter outside a container, either in a conda env or
within your distribution. For the former, you can run

```
conda create -n ds38 python=3.8
conda activate ds38
conda install -c conda-forge jupyterlab numpy pandas matplotlib
python -m pip install git+https://github.com/cython/cython
jupyter-lab
```


So I'm installing modern compilers and modifying defaults,
as the [guide says](https://www.scivision.dev/selecting-compiler-versions-with-update-alternatives/)

```
sudo apt install g++-10
```

## Requirements

1. First, you'll need the [NVIDIA HPC SDK](https://developer.nvidia.com/hpc-sdk), which
   provides the `nvc++` compiler. A minimum version of 20.9 is required to run these examples.
   Note that unless your NVIDIA driver supports CUDA 11.0, you will want to download the version
   that is bundled with two previous CUDA versions (10.1 and 10.2).
   
   Once installed, please ensure that the `nvc++` executable is in your PATH.

   Further, your GPU must have CUDA capability >= 6.0 to exploit `-stdpar` feature.

2. You will also need the development version of [Cython](https://github.com/cython/cython).
   The simplest way to get the minimum required version is to use `pip`:

   ```
   python -m pip install git+https://github.com/cython/cython@90684ac416f0349761074e242be4d981de40ce0f
   ```

3. Install Python dependencies:

   ```
   python -m pip install numpy pandas matplotlib
   ```

4. This step is optional. To run the CPU Parallel benchmarks, you will need `gcc >= 9.1`
   as well as the [TBB](https://github.com/oneapi-src/oneTBB) library. On Ubuntu 20.04
   `gcc-9` should already be the default, and I did `apt install libtbb-dev` to get
   TBB.
