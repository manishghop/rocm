## Enabling cuda on AMD GPU card

**Note**: Tested on following specifications:
  1) RAM 16GB
  2) OS: Ubuntu(22.04.02)LTS
  3) GPU: AMD Radeon 7800XT
  4) CPU: AMD Ryzen 5 5600x 6-core processor × 12

Steps:

- Install Ubuntu(22.04.02) freshly. I install it as a dual boot option(Win10 & Ubuntu).
- Install ROCm graphics driver depending on your card.
- I had a 7800xt so I installed [https://repo.radeon.com/amdgpu-install/23.30.2/ubuntu/jammy/amdgpu-install_5.7.50702-1_all.deb](url)
- Start a terminal & run below commands
- `sudo apt-get update`
- `sudo apt install amdgpu-install_5.7.50702-1_all.deb`
- After the gpu driveres are installed, you can check the gpu h/w info by running `rocminfo`
- I also installed anaconda as its much easier to create venv using conda
- Run [curl -O https://repo.anaconda.com/archive/Anaconda3-2023.09-0-Linux-x86_64.sh](url)
- Mostly curl won't be installed in your fresh ubuntu, so run `sudo apt install curl`
- Then run `bash ~/Downloads/Anaconda3-2020.05-Linux-x86_64.sh`
- There will be prompt to accept the license agreement do that.
- Run `source <PATH_TO_CONDA>/bin/activate` [Need to replace the PATH_TO_CONDA to your conda path].
- Hit `conda init`, this will take you to anaconda shell
- Now, we need to create a conda env with rocm enabled
- Run `conda create -n rocm python=3.10`
- Run `conda activate rocm`
- Since we have rocm 5.7 installed,
- Run `pip install --pre torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/rocm5.7`
- To test cuda is available in pytorch, open a python shell, then run following commands:</br>
  `import torch`</br>
  `torch.cuda.is_available()`</br>
  Expected behavior --> True, If it returns True, we are good to proceed further. But If not, that means you haven't installed the rocm gpu drivers properly, you'd have to repeat the steps all over again.
  We have to run one more test to check if cuda has been enabled.
- open Python shell by running `python`
  ```
  $ python
  >>> import torch
  >>> torch.rand(3,3).to("cuda")
  ```
  -> Expected behavior: </br>
  ```
  torch.tensor([],device="cuda:0")
  ```
  [If you get this, it means we have cuda up & running]. 
- But If it results in a HIP error(which is most certain If you're not using a 7900xt), to fix it:
- Simply run `export HSA_OVERRIDE_GFX_VERSION=11.0.0`[We are trying to override the graphic driver version to match 7900xt].</br>The gfx version for 7900xt currently supported is `gfx1100+` for rocm 5.7.
- Now again test the cuda available process, it should fix the error.
