# TabDDPM: Modelling Tabular Data with Diffusion Models
This is the official code for our paper "TabDDPM: Modelling Tabular Data with Diffusion Models" ([paper](TBA))

<!-- ## Results
You can view all the results and build your own tables with this [notebook](notebooks/Reports.ipynb). -->

## Setup the environment
1. Install [conda](https://docs.conda.io/en/latest/miniconda.html) (just to manage the env).
2. Run the following commands
    ```bash
    export REPO_DIR=/path/to/the/code
    cd $REPO_DIR

    conda create -n tddpm python=3.9.7
    conda activate tddpm

    pip install torch==1.10.1+cu111 -f https://download.pytorch.org/whl/torch_stable.html
    pip install -r requirements.txt

    # if the following commands do not succeed, update conda
    conda env config vars set PYTHONPATH=${PYTHONPATH}:${REPO_DIR}
    conda env config vars set PROJECT_DIR=${REPO_DIR}

    conda deactivate
    conda activate tddpm
    ```

## Running the experiments

Here we describe the neccesary info for reproducing the experimental results.  
Use `agg_results.ipynb` to print results for all dataset and all methods.

### Datasets

We upload the datasets used in the paper with our train/val/test splits (link below). We do not impose additional restrictions to the original dataset licenses, the sources of the data are listed in the paper appendix. 

You could load the datasets with the following commands:

``` bash
conda activate tddpm
cd $PROJECT_DIR
wget "https://www.dropbox.com/s/rpckvcs3vx7j605/data.tar?dl=0" -O data.tar
tar -xvf data.tar
```

### File structure
`tab-ddpm/` -- implementation of the proposed method  
`tuned_models/` -- tuned hyperparameters of evaluation model (CatBoost or MLP)

All main scripts are in `scripts/` folder:

- `scripts/pipeline.py` are used to train, sample and eval TabDDPM using a given config  
- `scripts/tune_ddpm.py` -- tune hyperparameters of TabDDPM
- `scripts/eval_[catboost|mlp|simple].py` -- evaluate synthetic data using a tuned evaluation model or simple models
- `scripts/eval_seeds.py` -- eval using multiple sampling and multuple eval seeds
- `scripts/eval_seeds_simple.py` --  eval using multiple sampling and multuple eval seeds (for simple models)
- `scripts/tune_evaluation_model.py` -- tune hyperparameters of eval model (CatBoost or MLP)
- `scripts/resample_privacy.py` -- privacy calculation  

Experiments folder (`exp/`):
- All results and synthetic data are stored in `exp/[ds_name]/[exp_name]/` folder
- `exp/[ds_name]/config.toml` is a base config for tuning TabDDPM
- `exp/[ds_name]/eval_[catboost|mlp].json` stores results of evaluation (`scripts/eval_seeds.py`)  

To understand the structure of `config.toml` file, read `CONFIG_DESCRIPTION.md`.

Baselines:
- `smote/`
- `CTGAN/` -- TVAE
- `CTAB-GAN/`
- `CTAB-GAN-Plus/`

### Examples

<ins>Run TabDDPM tuning.</ins>   

Template and example (`--eval_seeds` is optional): 
```bash
python scripts/tune_ddpm.py [ds_name] [train_size] synthetic [catboost|mlp] [exp_name] --eval_seeds
python scripts/tune_ddpm.py churn2 6500 synthetic catboost ddpm_tune --eval_seeds
```

<ins>Run TabDDPM pipleine.</ins>   

Template and example  (`--train`, `--sample`, `--eval` are optional): 
```bash
python scripts/pipleine.py --config [path_to_your_config] --train --sample --eval
python scripts/pipleine.py --config exp/churn2/first_exp/config.toml --train --sample
```

<ins>Run evaluation over seeds</ins>   

Template and example: 
```bash
python scripts/eval_seeds.py --config [path_to_your_config] [n_eval_seeds] ddpm synthetic [catboost|mlp] [n_sample_seeds]
python scripts/pipleine.py --config exp/churn2/first_exp/config.toml 10 ddpm synthetic catboost 5
```