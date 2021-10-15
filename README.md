# SPORCO-CUDA WSL INSTRUCTION

[
](http://sporco-cuda.readthedocs.io/en/latest/?badge=latest)

Before you start, you want to make sure you have met the minimum requirements for installing GPU powered Windows Subsystem for Linux(WSL) on your local windows machine. 

If you are curious about how using GPU through WSL is possible, please step to [CUDA on WSL User Guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html) by Nvidia. 

## WSL2 System Installation

WSL is only available on Windows for Windows Insider Program member. To sign up and install WSL2, you want to regiter the [Windows Insider Program](https://insider.windows.com/en-us/getting-started#register) and make sure you select **Release Preview Channel** as your flighting channel to get the lastest build. 

You want to make sure that your windows OS version is higher than **Windows 10 Build 19043.1263 (21H1)** by running the command  `winver` via Powershell or `win+R` combo key. In my case, I have **OS build 22471.1000** as shown below.  

![1](https://tva1.sinaimg.cn/large/008i3skNgy1gvcqw1yk5kj60fu09n75g02.jpg)

Before installing the WSL2, you still have a couple of house cleaning items you want to do. 

### Enable the Windows Subsystem for Linux(WSL)

Open Powershell in the Administration mode and run: 

`dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart`

### Enable Virtual Machine Feature

Open Powershell in the Administration mode and run: 

`dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart`

### Set the existing WSL to WSL2. 

For more details about the difference between WSL and WSL2, please step to [Comparing WSL 1 and WSL 2](https://docs.microsoft.com/en-us/windows/wsl/compare-versions). For the rest of this document, we will assume WSL2 is installed and use WSL and WSL2 interchangeably. 

Open Powershell in the Administration mode and run: 

`wsl --set-default-version 2` 

![2](https://tva1.sinaimg.cn/large/008i3skNgy1gvcrsga8bbj60qo0e6wfe02.jpg)



​		You might need to run the command in Powershell via the Administration mode in case you are not seeing the operation 	completed successfully message. 

​		`wsl --update`

### Install Linux distribution of your choice

Here we install Ubuntu-18.04 but you can see a full list of distributions by running `wsl --list -- online`

Open Powershell in the Administration mode and run to install: 

` 	wsl --install -d Ubuntu-18.04`and of course, you want to set your login information once the Ubuntu installer prompts you to do. 

1. Verify WSL 2 & Linux distribution :

   ​	`wsl --list --verbose`

   ![3](https://tva1.sinaimg.cn/large/008i3skNgy1gvcs26eea7j60qo0f10uf02.jpg)

   

   Before you move to the next section, you also want to make sure you are on the latest WSL kernel (**> 4.9.121**) by running command `wsl cat /proc/version `. Here my kernel version is **5.10.60.1**.

   ![4](https://tva1.sinaimg.cn/large/008i3skNgy1gvct2nhksuj60qo0e63zo02.jpg)

   ## WSL2 Compatible CUDA Driver Installation	

   You can download the WSL2 compatible CUDA driver from [NVIDIA Drivers for CUDA on WSL, including DirectML Support](https://developer.nvidia.com/cuda/wsl/download) where you can download the driver based on your OS and the type of your GPU in your system: **GeForce** or **Quadro**. 

   This is the **ONLY** CUDA driver you will need to install. After the installation, you can verify the stauts by 

   Open Powershell in the Administration mode and run to install: 

   `nvidia-smi` and `wsl nvidia-smi`. The first command verifise you can access GPU in the Windows system and the second command comfirms that you can acccess the same GPU from the WSL. 

   ![5](https://tva1.sinaimg.cn/large/008i3skNgy1gvczttyw94j60qo0scjxh02.jpg)



## CUDA Toolkit Installation on WSL2 

To complie & run a CUDA application, you will need to install **ONLY** the CUDA Toolkit (Agian, please **do not** install the driver again). You can install any version of the CUDA Toolkit you perfer as long as the CUDA Toolkit version is not higher than the CUDA version you installed previously. Here we will be installing the CUDA Toolkit 10.1 for illustration, but for more details & installation of other version please [CUDA Toolkit Downloads](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0).

Check the WSL2 bit architecture via command `wsl arch` from PowerShell in the Administrator mode.

`wsl` lets you get into the installed Linux distribution, and here we can see I installed the 64-bit version of the Ubuntu-18.04.

For other basic WSL2 commands, please go to [WSL Basic Command](https://docs.microsoft.com/en-us/windows/wsl/basic-commands). In short, you can step into the linux via `wsl` command and for simplicity in this tutorial, I will assume that you have already fired up WLS2. 

![6](https://tva1.sinaimg.cn/large/008i3skNgy1gvctxalfbdj60qo0e6aah02.jpg)

### Navigate to the `home` directory

1. `cd /`
2. `cd home/`

### Download & install CUDA Toolkit 10.1 using meta package

The following commands can be followed to ease the installation

1. `sudo apt update`

2. `sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub`

3. 

   ```
   sudo sh -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list'
   ```

4. `sudo apt update`

5. `sudo apt install cuda-toolkit-10-1`

### Update environment path for CUDA Toolkit 

1. Open the `.bashrc` file with the command`sudo vim ~/.bashrc`

2. Include the following path variables

   `export PATH=/usr/local/cuda-10.1/bin${PATH:+:${PATH}}`
   
   `export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}`

3. Relaunch the WSL2 by closing and restarting WSL2. 

   Verify the installtion of CUDA toolkit with the command `nvcc --version`

   ![8](https://tva1.sinaimg.cn/large/008i3skNgy1gvcxnap7qmj60qo04sq3n02.jpg)

### Build & compile CUDA samples 

For sanity check that you have gotten everything installed & configured correctly up to this point. You can attempt running a CUDA sample with the following commands.

1. `cd /usr/local/cuda-10.1/samples/4_Finance/BlackScholes`
2. `sudo make BlackScholes `
3. `./BlackScholes`

You should be able to see the following once the compile process & run process are done. 

![9](https://tva1.sinaimg.cn/large/008i3skNgy1gvcxn8ozutj60qo0scahe02.jpg)



## SPORCO & SPORCO CUDA Extension Installation

### SPORCO Install

The following commands will be used to install **SPORCO** and its dependencies.

1. `sudo apt install python3-pip`
2. `sudo -H pip3 install sporco`

Verify the installation via `pip3 list` command. 

![10](https://tva1.sinaimg.cn/large/008i3skNgy1gvczu73qvzj60qo0scq6h02.jpg)



### SPORCO-CUDA Installation

`sudo apt-get -y install cython python3-numpy python3-pip python3-future python3-pytest`

`sudo -H pip3 install pytest-runner sporco`

`sudo -H pip3 install cython`

#### Error Case 1: Solution TBD

`sudo -H CUDAHOME=/usr/local/cuda-10.1 pip3 install sporco-cuda`

![11](https://tva1.sinaimg.cn/large/008i3skNgy1gvczu91g0yj60qo0scgyt02.jpg)

Failed Attempt: `sudo apt-get install python3-dev`



#### Error Case 2: Solution TBD  

`sudo -H CUDAHOME=/usr/local/cuda-10.1 python3 sporco-cuda build`

![12](https://tva1.sinaimg.cn/large/008i3skNgy1gvczubjsf6j60qo0sc7iy02.jpg)

