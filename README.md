# 4dvarnet-james-uv-ssc

This repository implements the experiments described in the following research
paper:

> Fablet, R, Chapron, B, Sommer, J, Sévellec, F. Inversion of sea surface
> currents from satellite-derived SST-SSH synergies with 4DVarNets.
> ⟨[arxiv](https://arxiv.org/abs/2211.13059)⟩

See the repository [4dvarnet-core](https://github.com/CIA-Oceanix/4dvarnet-core)
for more informations about 4DVarNet.


## Installation

### Prerequisite

- git
- conda


### Instructions

- Clone repo:
    ```
    git clone https://github.com/CIA-Oceanix/4dvarnet-james-uv-ssc.git
    ```

- Install environment:
    ```
    conda create -n 4dvarnet mamba python=3.9 -c conda-forge
    conda activate 4dvarnet
    mamba env update -f environment.yaml
    ```

### Download the data

The data are available at:
```
# obs
https://s3.eu-central-1.wasabisys.com/melody/NATL/data/gridded_data_swot_wocorr/dataset_nadir_0d_swot.nc

# oi
https://s3.eu-central-1.wasabisys.com/melody/NATL/oi/ssh_NATL60_swot_4nadir.nc

# ref
https://s3.eu-central-1.wasabisys.com/melody/NATL/ref/NATL60-CJM165_NATL_ssh_y2013.1y.nc
https://s3.eu-central-1.wasabisys.com/melody/NATL/ref/NATL60-CJM165_NATL_sst_y2013.1y.nc
https://s3.eu-central-1.wasabisys.com/melody/NATL/ref/NATL60-CJM165_NATL_u_y2013.1y.nc
https://s3.eu-central-1.wasabisys.com/melody/NATL/ref/NATL60-CJM165_NATL_v_y2013.1y.nc
```

You must modify the file `hydra_config/file_paths/paths.yaml` so each
path points to the specified dataset.


### Run

#### Train

The four experiments described in the research paper can be reproduced with the
following config files (located at `hydra_config/xp/`):

```
4DVarNet-SSH-only
4DVarNet-SSH-SST
U-Net-SSH-only
U-Net-SSH-SST
```

If you want to start a training, execute the following command:

```
python hydra_main_uv.py entrypoint=train xp=XXX
```

Where `XXX` is one of the experiment files listed above.

The outputs will be stored in the directory `dashboard` (which will be
automatically created if non existent).

#### Test

Once the training session is done, checkpoints of the models will be
generated in the output directory. If you want to start a test session
with one of these checkpoint, use the following command:

```
python hydra_main_uv.py entrypoint=test xp=XXX entrypoint.ckpt_path="YYY"
```

Where `XXX` is one of the experiment files listed above and `YYY` is
the path to the checkpoint you want to load (a relative path from the directory
`dashboard`). The name of the checkpoint files usually contains the character
equal "=", which must be escaped with a backslash "\\". For example:

```
... entrypoint.ckpt_path="version_8/checkpoints/my_checkpoint_epoch\=161-val_loss\=3.56.ckpt"
```

The checkpoints used to generate the test results in the research paper are
available in the directory `TrainedModels`. So you just need to load them in the
test command described above. For example:

```
python hydra_main_uv.py entrypoint=test entrypoint.ckpt_path="../TrainedModels/4DVarNet-SSH-SST.ckpt" xp=4DVarNet-SSH-SST
```

Decimals of the scores might slightly differ from those of the research
paper because of some changes in the code, the rounding or the precision
of decimals, seed, etc.

The table below shows the association between the notations in the
research paper and the names of the scores in the output of a test
session.

| Metric name in the paper  | Metric name in the output |
|:-------------------------:|:-------------------------:|
| $\lambda_{x, u}$          | `test_lambda_x_u`         |
| $\lambda_{x, v}$          | `test_lambda_x_v`         |
| $\lambda_{t, u}$          | `test_lambda_t_u`         |
| $\lambda_{t, v}$          | `test_lambda_t_v`         |
| $\tau_{u, v}$             | `test_var_mse_uv`         |
| $\tau_{\text{vort}}$      | `test_var_mse_curl`       |
| $\tau_{\text{div}}$       | `test_var_mse_div`        |
| $\tau_{\text{strain}}$    | `test_var_mse_strain`     |


## Preprints and Software License
Associated preprints: https://arxiv.org/abs/2211.13059
License: CECILL-C license

Copyright IMT Atlantique/OceaniX, contributor(s) : M.M. Amar, M. Beauchamp, R. Fablet, Q. Febvre (IMT Atlantique), B. Carpentier (CLS) 21/03/2020

Contact person: ronan.fablet@imt-atlantique.fr
This software is a computer program whose purpose is to apply deep learning
schemes to dynamical systems and ocean remote sensing data.
This software is governed by the CeCILL-C license under French law and
abiding by the rules of distribution of free software.  You can  use,
modify and/ or redistribute the software under the terms of the CeCILL-C
license as circulated by CEA, CNRS and INRIA at the following URL
"http://www.cecill.info".
As a counterpart to the access to the source code and  rights to copy,
modify and redistribute granted by the license, users are provided only
with a limited warranty  and the software's author,  the holder of the
economic rights,  and the successive licensors  have only  limited
liability.
In this respect, the user's attention is drawn to the risks associated
with loading,  using,  modifying and/or developing or reproducing the
software by the user in light of its specific status of free software,
that may mean  that it is complicated to manipulate,  and  that  also
therefore means  that it is reserved for developers  and  experienced
professionals having in-depth computer knowledge. Users are therefore
encouraged to load and test the software's suitability as regards their
requirements in conditions enabling the security of their systems and/or
data to be ensured and,  more generally, to use and operate it in the
same conditions as regards security.
The fact that you are presently reading this means that you have had
knowledge of the CeCILL-C license and that you accept its terms.