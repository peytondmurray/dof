# Dept. of Forestry

Goal of this demo project is to showcase:
* checkpointing environments
* pushing/pulling environments (at certain checkpoints)

## Dev env

To setup your dev env, create a conda env

```
$ conda env create -f environment.yml 

$ conda activate dof-dev
```

## Try it out

### `dof lock`
This will generate a lockfile for a given environment file

```
$ dof lock --env-file demo-assets/env1.yml
```

### `dof checkpoint`
This command will checkpoint your environment. That is, take a snapshot of
what is currently installed so that you can go back to it at any time.

```
$ dof checkpoint save
```

To list all the current available checkpoints run

```
$ dof checkpoint list
```

To see all the changes to the environment since your last checkpoint

```
$ dof checkpoint diff --rev <revision uuid>
```

#### Example

Start with the `dof-dev` environment
```
$ conda env create -f environment.yml 
. . .
$ conda activate dof-dev

# ensure dof is installed
$ python -m pip install -e .
```

Now, try to create some checkpoints and install some packages
```bash
# createa a checkpoint
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint save  
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint list              
                                                 Checkpoints                                                  
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ uuid                             ┃ tags                                 ┃ timestamp                        ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ 1bc3a3a434454437a9f72061672b8189 │ ['1bc3a3a434454437a9f72061672b8189'] │ 2025-01-25 00:00:07.707080+00:00 │
└──────────────────────────────────┴──────────────────────────────────────┴──────────────────────────────────┘

# install a new package
(dof-dev) sophia:dof/ (main✗) $ conda install jinja2  

# check the diff
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint diff --rev 1bc3a3a434454437a9f72061672b8189            
diff with rev 1bc3a3a434454437a9f72061672b8189
+ url='https://conda.anaconda.org/conda-forge/linux-64/markupsafe-3.0.2-py312h178313f_1.conda'
+ url='https://conda.anaconda.org/conda-forge/noarch/jinja2-3.1.5-pyhd8ed1ab_0.conda'

# save a new checkpoint
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint save 
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint list     
                                                 Checkpoints                                                  
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ uuid                             ┃ tags                                 ┃ timestamp                        ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ d1256c34d93c48eb96ccfb125c598ffe │ ['d1256c34d93c48eb96ccfb125c598ffe'] │ 2025-01-25 00:02:44.065559+00:00 │
│ 1bc3a3a434454437a9f72061672b8189 │ ['1bc3a3a434454437a9f72061672b8189'] │ 2025-01-25 00:00:07.707080+00:00 │
└──────────────────────────────────┴──────────────────────────────────────┴──────────────────────────────────┘

# try to install a pip package
(dof-dev) sophia:dof/ (main✗) $ python -m pip install blinker

# check the diff from the last checkpoint
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint diff --rev d1256c34d93c48eb96ccfb125c598ffe     
diff with rev d1256c34d93c48eb96ccfb125c598ffe
+ name='blinker' version='1.9.0' build='pypi_0' url=None

# check the diff from the first checkpoint
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint diff --rev 1bc3a3a434454437a9f72061672b8189  
diff with rev 1bc3a3a434454437a9f72061672b8189
+ name='blinker' version='1.9.0' build='pypi_0' url=None
+ url='https://conda.anaconda.org/conda-forge/linux-64/markupsafe-3.0.2-py312h178313f_1.conda'
+ url='https://conda.anaconda.org/conda-forge/noarch/jinja2-3.1.5-pyhd8ed1ab_0.conda'

# remove a dependency
(dof-dev) sophia:dof/ (main✗) $ conda uninstall jinja2

# now recheck the diff from the last checkpoint
(dof-dev) sophia:dof/ (main✗) $ dof checkpoint diff --rev d1256c34d93c48eb96ccfb125c598ffe    
diff with rev d1256c34d93c48eb96ccfb125c598ffe
+ name='blinker' version='1.9.0' build='pypi_0' url=None
- url='https://conda.anaconda.org/conda-forge/linux-64/markupsafe-3.0.2-py312h178313f_1.conda'
- url='https://conda.anaconda.org/conda-forge/noarch/jinja2-3.1.5-pyhd8ed1ab_0.conda'
```
