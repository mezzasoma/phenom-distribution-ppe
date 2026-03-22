# phenom-distribution-ppe-public
This repository contains the scripts and environment setup used to carry out the parameter estimation in [_Impact of numerical-relativity waveform calibration on parametrized post-Einsteinian tests_](https://arxiv.org/abs/2603.15765). It interfaces [`ripplegw`](https://github.com/tedwards2412/ripple) (waveform generation) with [`bilby`](https://github.com/bilby-dev/bilby) (Bayesian inference).

## Quick start
1. Clone this repository on both the cluster and your laptop. It is designed to run independently in each location:
   - Laptop: intended for file generation, code development, and postprocessing (An IPython kernel for Jupyter notebook is created during installation).
   - Cluster: intended for running the parameter-estimation jobs across multiple nodes.
2. Populate `config_example.yaml` and rename it to `config.yaml`.
3. From within the repository `phenom-distribution-ppe-public`, create the conda environment by running
    ```
    bash conda/create_phenom_distribution_ppe.sh 
    ```
    This can take several minutes.
4. Activate the newly-installed conda environment `phenom-distribution-ppe` with
    ```
    conda activate phenom-distribution-ppe
    ```
5. Download the noise power spectral density (O5) for LIGO and Virgo, then generate injection, prior, and `parallel-bilby` configuration files by running
    ```
    cd data/analysis/bilby-inference
    python create_psd.py
    python create_injection.py
    python create_prior.py
    python create_configuration.py
    ```
6. Start parameter estimation with `bilby` using the launch script. For example:
    ```
    bash ../../../scripts/bilby-inference/launch_parallel_bilby.sh configuration/inj-ripple-rec-ripple-ppe.ini prior/ripple_delta_at_IMRPhenomD_total_mass_60.0_mass_ratio_0.434_no_ppe.prior injection/ripple_flowmc-20240722113944_furthest_point_from_IMRPhenomD_total_mass_60.0_mass_ratio_0.434_chi_1_-0.60_chi_2_-0.60_SNR_100.0.inj
    ```
    See usage notes with 
    ```
    bash ../../../scripts/bilby-inference/launch_parallel_bilby.sh --help
    ```
7. To generate corner plots, use
    ```
    python postprocess_bilby_run.py
    ```
## Requirements
- Laptop: [Anaconda 2024.06](https://docs.conda.io/en/latest/)
- Cluster: [Anaconda 2024.06](https://docs.conda.io/en/latest/), [Slurm 25.05.5](https://slurm.schedmd.com/quickstart.html), [Open MPI v5.0.1](https://docs.open-mpi.org/en/v5.0.x/)

## Note
This setup is configured to use [`parallel-bilby`](https://git.ligo.org/lscsoft/parallel_bilby) to distribute sampling across multiple nodes on a computing cluster. However, if multi-node parallelization is not needed, this setup can still be used to run regular [`bilby`](https://github.com/bilby-dev/bilby) (see [examples](https://github.com/bilby-dev/bilby/tree/main/examples/gw_examples/injection_examples)) on a single node by manually submitting a job through Slurm.