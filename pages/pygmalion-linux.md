---
layout: default
title: Running Pygmalion on Linux
nav_order: 1
description: "How to setup Pygmalion on Linux-based systems"
---


# Running Pygmalion on Linux Operating Systems

## Glossary
This section is for terms that might be unknown to the average user inexperienced in running AI models.

- **CUDA:**  
A library created by NVIDIA to use compute on their GPUs. Only applies to NVIDIA cards. Use [this list](http://developer.nvidia.com/cuda-gpus) to check if your GPU is compatible.

- **ROCm:**  
Similar to CUDA but for AMD cards. Linux support is available, but the majority of consumer grade AMD cards don't support it. [This list](https://github.com/ROCm/ROCm.github.io/blob/master/hardware.md) contains all the AMD GPUs currently supported.

- **Kobold or KAI:**  
KoboldAI is an application and runtime to load language models easily.  

- **TavernAI or TAI:**  
A graphical application made specifically to have chats using language models. It can run locally or using a remote server. Can act as a frontend for KAI with a nice-looking UI.

- **VRAM:**
The amount of storage your GPU has access to - this is much faster than your system RAM. You can figure out the exact amount on your card by running `nvidia-smi` on NVIDIA machine. On AMD cards, it's not possible to the same without ROCm installed - you can use `lspci | grep VGA` to find out your card's name, then look it up on the internet for its specifications.

- **Parameters:**
A model is a set of mathematical equations that help to make predictions or solve problems. In machine learning, a model is trained on a dataset and uses this data to learn how to make predictions. The parameters of a model are the variables that control its behavior. These parameters are learned during the training process, and are used to make predictions on new data. The number of parameters in a model determines its complexity and affects its accuracy and computational cost.


## System Requirements:


**TL;DR**:

| Model | VRAM  |
|:------|:------|
| 6B    | 16GB  |
| 2.7B  | 6.5GB |
| 1.3B  | 4GB   |


The Pygmalion models come in three sizes: 1.3 billion, 2.7 billion, and 6 billion parameters. The size of the model determines the amount of VRAM required to run it: 4GB, 6.5GB, and 12GB respectively. But keep in mind that you'll need 3-4GB of extra memory for processing and storage.

The 6 billion model offers the best performance, but the 2.7 billion model may be enough for your needs and is easier to run locally without using Colab. Keep in mind that models below 6B are currently outdated, and as of now, there aren't any plans to update them, since the focus is solely on 6B.

If you have an NVIDIA GPU, you should be able to meet the VRAM requirements. However, if you have an AMD or Intel GPU, things are different. AMD users can check if their card is compatible with ROCm and supported by high-end RDNA2 or RDNA3 cards. Unfortunately, Intel GPU users may run into issues as PyTorch on OpenCL is not supported. In that case, it's best to use a cloud-based solution.

You can also run the 6B model on 8GB of VRAM (provided your card supports CUDA or ROCM) by assigning ~12 layers to the model in KAI, however the inference time will be around 50 seconds for 90 tokens, depending on your CPU and system RAM speeds.


## Installing Dependencies
- Install `git` with your distro's package manager.

### NVIDIA
Install `cuda`. Your package manager may or may not have it, so I'll detail the installation here for the popular base distros:
- Arch/Manjaro: The easiest of them all. Simply run `sudo pacman -S cuda`.

- Debian/Ubuntu: Run `apt update && apt install build-essentials && apt install nvidia-cuda-dev nvidia-cuda-toolkit`

- Fedora: Harder than most. Too complicated to include here, so follow [the official Fedora guide](https://fedoraproject.org/wiki/Cuda) on how to install it.

- openSUSE: ***no official guide, and this method is untested, proceed with caution:*** 
```
zypper addrepo -p 100 http://developer.download.nvidia.com/compute/cuda/repos/opensuse15/x86_64/cuda-opensuse15.repo
zypper ref
zypper install cuda-libraries-10-2 cuda-libraries-dev-10-2 cuda-compiler-10-2
```
You may need to change version numbers.

**Confirm the installation's success by running `nvcc --version`**

### AMD

- Arch Linux: Easiest again. Install [paru](https://aur.archlinux.org/packages/paru) or your favorite AUR helper. Then run this:
```
paru -S rocm-hip-sdk rocm-opencl-sdk
```
- Manjaro: Same as Arch Linux, but you can use the built-in AUR/Package manager, `pamac`:
```
pamac build rocm-hip-sdk rocm-opencl-sdk 
```

- Other distros: Too complicated to include here, please follow the [official AMD guide](https://rocmdocs.amd.com/en/latest/Installation_Guide/Installation_new.html).
<!-- I don't have an AMD GPU to test this out. But it should work. -->



## Installing KoboldAI
KoboldAI has the ability to be used for conversational purposes, but its effectiveness is limited compared to TavernAI. It also does not have the ability to efficiently transfer input to the Pygmalion model as intended. As a result, the primary use of KoboldAI is to facilitate a connection between TavernAI and the Pygmalion model.

- Create a directory for Pygmalion; we will install both Tavern and Kobold here: `mkdir pygmalion`
- Clone the KoboldAI repository:
```bash
git clone https://github.com/henk717/KoboldAI && cd KoboldAI
```

### Starting KoboldAI

- Run the `play.sh` for NVIDIA and `.play-rocm.sh` for AMD:
```
./play.sh
./play-rocm.sh
```
***NOTE: If this doesn't work for you, and results in infinite loading, scroll down. I'll write a section for setting up Kobold and loading the models manually.***

***NOTE 2: The download size for KAI is roughly 2GB, so it will take a while depending on your bandwidth.***

Once you have the browser tab open on Kobold:
- Click on `AI` in the top-left
- Click on `Chat Models`
- Select the Pygmalion model you wish to load then click on `Load`
  > You can look at the terminal window to see the download progress.
- Once the download is finished, the model will be loaded to your GPU.
You can now leave Kobold alone, even close the browser tab, as long as the terminal is open, it will be available for Tavern to use.

### Optional: Setting up KoboldAI manually (skip if running `.play.sh` or `.play-rocm.sh` had no issues)

**Open a terminal window and `cd` into the KoboldAI folder.**

**For NVIDIA cards, run these commands in the directory:**
```
wget -qO- https://micromamba.snakepit.net/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
bin/micromamba create -f environments/huggingface.yml -r runtime -n koboldai -y
bin/micromamba create -f environments/huggingface.yml -r runtime -n koboldai -y
./play.sh
```
**For AMD cards, run these commands in the directory:**
```
wget -qO- https://micromamba.snakepit.net/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
bin/micromamba create -f environments/rocm.yml -r runtime -n koboldai-rocm -y
bin/micromamba create -f environments/rocm.yml -r runtime -n koboldai-rocm -y
./play-rocm.sh
```
*It is necessary to execute the second command twice during the installation process for both NVIDIA and AMD systems, as there is an issue with micromamba that causes it to fail on the initial run. However, it is important to note that the necessary downloads will not need to be repeated during the second execution.*

**Loading Pygmalion models manually (optional, KAI interface can automatically download any model):**

Download the models from the HF repository, and place them in `KoboldAI/models/pygmalion-6b`:

[pygmalion-6b (main branch)](https://huggingface.co/PygmalionAI/pygmalion-6b/tree/main)

[pygmalion-6b (dev branch)](https://huggingface.co/PygmalionAI/pygmalion-6b/tree/dev)



## Installing TavernAI
TavernAI is a chat-based interface that allows easy communication with AI models, including Kobold. It provides a user-friendly and intuitive experience.

- Install `nodejs` through your package manager.
- Clone the TAI repository in the same directory KAI was cloned:
```
git clone https://github.com/TavernAI/TavernAI && TavernAI
```

### Starting TavernAI and linking it to KoboldAI
- In terminal, run `npm i && node server.js`
- The above step will output a URL, usually `http://127.0.0.1:8000`. Enter this URL in your browser.
- You're now inside TavernAI. You need to connect it to Kobold now. Do the following:
- Click on the 3 vertical lines to the top right corner of the TAI screen.
- Click on `Settings`.
- Enter your KoboldAI URL in the `API url` section. Add a `/api` at the end of the url: `http://127.0.0.1:5000/api` or `localhost:5000/api`.
- Click `Connect` and you're done!
  > You'll have to manually click connect each time you close and reopen or refresh the TavernAI tab.
- You can now close out of settings and use Tavern! Or you can go to Characters, and create characters. Note that the default character, Chloe, cannot be interacted with.

#### Keep in mind that for 16GB Cards, the context size in TavernAI's settings need to be lowered to 1400, in order to prevent OOM (out of memory) issues.

## A quick recap for the next time!
- Go to your KoboldAI folder, run `./play.sh` in Terminal.
- Click `AI` then `Chat models`, then the model you wish to load, then finally click `Load`
- Wait for it to load.
- Kobold can now be left alone.
- Go to your TavernAI folder, run `node server.js` in Terminal.
- Open http://127.0.0.1:8000 or localhost:8000 in your browser. 
- Click the three vertical lines in the top-right, and go to `Settings`.
- Click `Connect` next to the API url box.

## Alternatively, here's a shell script to automate running KoboldAI & TavernAI, along with a script to shut them down:

**The start script:** (replace `play.sh` with `play-rocm.sh` for AMD)
```
cd KoboldAI
git pull &
source play.sh --model PygmalionAI/pygmalion-6b --revision main &
cd ../TavernAI
git pull &
node server.js &

```
**The stop script:**
```
pkill -9 -f "node server"
pkill -9 -f "aiserver"
```


## Adding characters
If you want to add existing character templates from the community:

You can find characters at the [rentry](https://rentry.org/pygbotprompts#tavernai-characters) or the [official discord](https://discord.gg/bv4xczmcyk):

- Open TavernAI and navigate to `Characters`
- Click on the `+Import` button, this is located right next to the `+` button used for creating a new character.
- You're done.


## Softprompts
Softprompts in language models are a way to provide a model with some context or a starting point before it generates text. In the case of PygmalionAI, the majority softprompts available are related to specific anime series or topics. By providing a softprompt, the model can generate text that is more relevant and focused on the specific context provided. This is particularly useful for those who want to generate conversations or dialogues about their favorite series. The softprompt helps the model generate text that is more specific to the anime fan's interests and tastes.

- Download your softprompt of choice from the [official discord server](https://discord.gg/bv4xczmcyk).
- Add your softprompts in `.zip` format to the `KoboldAI/softprompts`.
- Make sure KoboldAI is running, and pygmalion is already loaded.
- An option for softprompts should appear in KoboldAI UI. There, you can load it in.
- Have fun! 

## Something broke!
First off, Google is your friend, most of Kobold and Tavern are really just a hodge-podge of existing popular libraries cobbled together into something that works. Meaning most errors you'll encounter, others probably have too.

If you can't figure out anything, [Come see us on the Official Discord!](https://discord.gg/bv4xczmcyk).
