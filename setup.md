---
title: Setup
---

{% include links.md %}

### Install Python

We use Python version 3.6.0, but any newer version should also work. There are many methods to setting up a Python environment but we'd recommend using some sort of virtual environment so as to not break your system Python install. Two methods (of many) are listed below:

#### Method 1: Setting up conda environment (easiest) [Windows, Linux, MacOS]
For easy set-up we recommend [Anaconda](https://www.anaconda.com/download/) to manage Python packages for scientific computing. Once installed, setting up the Python environment can be done quite easily:

##### Windows
1. Install Anaconda Python version 3.7
2. Open **Anaconda Navigator**
3. Click on **Environments** on the left pane
4. Click **Create** then type in `neuroimaging_venv`
5. In the`neuroimaging_venv` entry, click the play button, then click **Open Terminal** 
6. In terminal type: 
```
conda install -y numpy pandas scipy scikit-learn matplotlib jupyter ipykernel nb_conda
conda install -y -c conda-forge awscli
pip install nilearn nibabel
```
7. Close the terminal, click on the play button again and open **Jupyter Notebook**
8. Navigate to `neuroimaging_venv` folder you downloaded earlier.
9. Done!

##### Linux and MacOS

After installing Anaconda, open terminal and type: 

```
cd scwg2018_python_neuroimaging
conda create -p ./scwg2018_nilearn
source activate $(pwd)/scwg2018_nilearn
conda install numpy pandas scipy scikit-learn matplotlib jupyter ipykernel nb_conda
conda install -c conda-forge awscli
pip install nilearn nibabel

```
#### Method 2: Using pyenv (my favourite) [Linux, MacOS]
An alternative method uses [pyenv](https://github.com/pyenv/pyenv) with [pyenv virtualenv](https://github.com/pyenv/pyenv-virtualenv). This is a favourite because it seamlessly integrates multiple python versions and environments into your system while maintaining use of pip (instead of conda).
```
cd scwg2018_python_neuroimaging
pyenv virtualenv 3.6.0 scwg2018_nilearn
echo scwg2018_nilearn > .python-version
pip install --requirement requirements.txt
```

### Obtain lesson materials

This tutorial uses data derived from the **UCLA Consortium for Neuropsychiatric Phenomics LA5c Study [1]**.

To acquire the data we use [Amazon AWS S3](https://aws.amazon.com/). You can set up an account using the link. Then you'll need to set up the **awscli** python tool using your AWS account credentials (more info: [Amazon AWS CLI](https://aws.amazon.com/cli/))
```
aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: ca-central-1
Default output format [None]: ENTER
```
To download (**warning: large download size!**) the subset of the data used for the tutorial:

```
cd scwg2018_python_neuroimaging

# download T1w scans
cat download_list | \
  xargs -I '{}' aws s3 sync --no-sign-request \
  s3://openneuro/ds000030/ds000030_R1.0.5/uncompressed/{}/anat \
  ./data/ds000030/{}/anat

# download resting state fMRI scans
cat download_list | \
  xargs -I '{}' aws s3 sync --no-sign-request \
  s3://openneuro/ds000030/ds000030_R1.0.5/uncompressed/{}/func \
  ./data/ds000030/{}/func \
  --exclude '*' \
  --include '*task-rest_bold*'

# download fmriprep preprocessed anat data
cat download_list | \
  xargs -I '{}' aws s3 sync --no-sign-request \
  s3://openneuro/ds000030/ds000030_R1.0.5/uncompressed/derivatives/fmriprep/{}/anat \
  ./data/ds000030/derivatives/fmriprep/{}/anat

# download fmriprep preprocessed func data
cat download_list | \
  xargs -I '{}' aws s3 sync --no-sign-request \
  s3://openneuro/ds000030/ds000030_R1.0.5/uncompressed/derivatives/fmriprep/{}/func \
  ./data/ds000030/derivatives/fmriprep/{}/func \
  --exclude '*' \
  --include '*task-rest_bold*'
```
