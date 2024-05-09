# Coral USB Accelerator on Debian 12 (Bookworm)
**This installation is tested on a Raspberry Pi 4B with a fresh installation of Debian 12 Bookworm. It does not require manual recompiling any of the libraries or modules.**

Update the packages to the latest version.
  
    sudo apt update
    sudo apt full-upgrade

## Install pyenv

Install pyenv following the instructions on https://github.com/pyenv/pyenv:

    curl https://pyenv.run | bash

Add the following to the `.bashrc` and restart shell:

    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
    echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(pyenv init -)"' >> ~/.bashrc
    exec $SHELL

`pyenv` builds from source. Install Python build dependencies for installing different versions following the instructions on https://github.com/pyenv/pyenv/wiki#suggested-build-environment:
    
    sudo apt update
    sudo apt install build-essential libssl-dev zlib1g-dev \
      libbz2-dev libreadline-dev libsqlite3-dev curl \
      libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev

Check the possible python versions and install the latest compatible version:

    pyenv install --list
    pyenv install -v 3.9.18
    
Check the available installed versions and set the global python version to the installed version:

    pyenv versions
    pyenv global 3.9.18

## Install pipenv

Install pipenv and set the environment variable such that the virtual environment is always created inside the project folder (second step is not a necessity):

    pip install pipenv
    add export PIPENV_VENV_IN_PROJECT=1

## Install libedgetpu library and pycoral

Below follows from: https://coral.ai/docs/accelerator/get-started/

Install libedgetpu1-std library:

    echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    sudo apt update
    sudo apt install libedgetpu1-std

Now plug in the Coral USB Accelerator or if it was plugged in, remove it and plug in again.

Move to the directory, where you would like to have your project and create a virtual environment:

    cd ~/Documents
    mkdir coral && cd coral
    python -m venv .venv

Activate the virtual environment. This can be done either using `pipenv shell` or simply sourcing the activate script `source .venv/bin/activate`. 

Install the pycoral module from private url using pip:
    
    pip install --extra-index-url https://google-coral.github.io/py-repo/ pycoral~=2.0

## Test pycoral examples

Assuming you are still in the `coral` directory created above, pull the repository and change into the directory:

    git clone https://github.com/google-coral/pycoral.git
    cd pycoral

Get the dependencies of the examples:

    bash examples/install_requirements.sh

Run the image classifier example on https://coral.ai/docs/accelerator/get-started/#3-run-a-model-on-the-edge-tpu:

    python3 examples/classify_image.py \
      --model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite \
      --labels test_data/inat_bird_labels.txt \
      --input test_data/parrot.jpg


