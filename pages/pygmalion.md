---
layout: default
title: Running Pygmalion on Windows
nav_order: 1
description: "How to configure Pygmalion to run directly on your own hardware. This guide should hopefully be noob-proof."
---
# Running Pygmalion on Windows (mostly)

> If your computer is shit, go here [Running Pygmalion in the cloud](pygmalion-cloud)

{:toc}

## Glossary
{: .no_toc }
This section is intended to brief you on what all the technical jargon in this document means.

**CUDA:**  
A library created by NVIDIA to use compute on their GPUs. Anytime you see CUDA mentioned, assume it only applies to NVIDIA.  

**ROCm:**  
Similar to CUDA but for AMD cards. ROCm has less support than CUDA, as it's mostly made for professional enterprise cards, but some consumer cards are supported. Google is your friend on this one.  

**Kobold or KAI:**  
KoboldAI is an application and runtime to load language models easily.  

**TavernAI:**  
A graphical application made specifically to have chats using language models. It can run locally or using a remote server.  

**VRAM:**  
Video Memory is the RAM on your graphics card. It is much faster and higher bandwidth than the system RAM on your motherboard that connects to the CPU.
> In Windows Task Manager, you can go to the Performance tab, then click on your graphics card on the left. **Only the "Dedicated Video Memory" section matters.** The other values aren't useful, and do not represent any real amount of memory you may have.

## System Requirements:
Pygmalion is available in 1.3B, 2.7B and 6B variants. They represent the number of parameters in the model. The 1.3B variant needs at least 4GB of VRAM, the 2.7B variant needs at least 6.5GB of VRAM, and the 6B variant needs at least 12GB of VRAM.  
**These numbers say AT LEAST because some extra memory is required to store and process tokens. Usually around 3-4GB. Meaning these numbers are more like 8GB, 11GB and 16GB respectively.**

If you can spare the hardware for it, the 6B variant is a LOT better than the smaller ones. Though, the simplicity of running locally without dealing with Colab cannot be understated and the 2.7B model may be enough for many.

If you have an NVIDIA GPU with enough VRAM, you're good to go no matter what platform you're running.

With AMD or Intel (LOL, who the fuck has ARC?), the story is a bit different. For AMD, you'll want to check if your card is supported by ROCm High-end RDNA2 (RX 6000) and RDNA3 (RX 7000) cards are supported out-of-the-box, BUT ONLY ON LINUX.

With Intel cards, you're basically fucked. PyTorch on OpenCL ain't happenin' anytime soon and oneAPI won't save you. Just use the cloud.

## Shut the fuck up, I want to run funny AI waifu
OK fine, I get it.

### Installing KoboldAI:
- [Download KoboldAI](https://koboldai.org/windows) and run the installer.  
  Make sure you don't have a `B:` drive as that will be used for the installation.  
  **When you read the updater script, PICK OPTION 2.**
<!-- - Extract the ZIP to a drive you have at least 25GB of free space on. Using an SSD will speed up startup time for the model a lot.   -->
<!-- **Use a folder name without spaces at the root of the drive, e.g. `E:\KoboldAI`** -->
  <!-- > If you know your way around git, then just git clone, it'll make updates easier in the future too. -->
<!-- - In the extracted folder, run the `install_requirements.bat` file by double-clicking it. **Though it's recommended to right-click it and Run As Administrator.** Type `2` and press Enter. Then let it do it's thing. -->
<!-- - When it says `Press any key to continue.` then you're done installing Kobold. -->

### Starting KoboldAI
In that same folder, double-click on `play.bat` which will start Kobold.

It's important to Note, Kobold can technically be used for chat purposes. Though it is much less user-friendly than Tavern, and doesn't support sending your input to the model in the way Pygmalion likes.

so, for our purposes, Kobold is merely a way for Tavern to connect to the AI.

Once you have the browser tab open on Kobold:
- Click on `AI` in the top-left
- Click on `Chat Models`
- Select the Pygmalion model you wish to load then click on `Load`
  > You can look at the terminal window to see the download progress.
- Once the download is done, the model will be loaded onto your GPU.

You can now leave Kobold alone, even close the browser tab, as long as the terminal is open, it will be available for Tavern to use.

### Installing TavernAI
Tavern is more of a chat-oriented frontend for various AI runtimes, including Kobold.

First you will need to install NodeJS [using this download link](https://nodejs.org/download/release/v19.1.0/node-v19.1.0-x64.msi) the install should go pretty fast. It's just a *click next, next, next* kinda deal.

- [Download TavernAI](https://github.com/TavernAI/TavernAI/archive/refs/heads/main.zip) and extract it anywhere, it doesn't really matter.
- Run `start.bat`

### Using Tavern and connecting it to KoboldAI
- Once you run `start.bat` the NodeJS server will start.
- Open https://127.0.0.1:8000 in your browser.
- You're now in Tavern! but it's not connected.
- Click the hamburger menu (three lines) in the top-right of the page
- Click on Settings
- In the API url section, put in `http://127.0.0.1:5000/api`
- Click Connect.
  > You'll have to manually click connect each time you close and reopen or refresh the TavernAI tab.
- You can now close out of settings and use Tavern! Or you can go to Characters, and create characters.

### A quick recap for the next time!
- Go to your KoboldAI folder, run `play.bat`
- Click `AI` then `Chat models`, then the model you wish to load, then finally click `Load`
- Wait for it to load.
- Kobold can now be left alone.
- Go to your TavernAI folder, run `start.bat`
- Open http://127.0.0.1:8000 in your browser
- Click the three dots in the top-right, and go to `Settings`
- Click `Connect` next to the API url box.

## Adding characters
If you want to add existing character templates from the community:

You can find characters here: https://rentry.org/pygbotprompts#tavernai-characters

- Go to your Tavern AI folder
- Go to the public folder
- And finally in the characters folder
- Create a subfolder with the name of the character then drop the JSON file and the avatar in there.
- That's it! You'll be able to pick the character in the Tavern UI.

## Something broke!
First off, Google is your friend, most of Kobold and Tavern are really just a hodge-podge of existing popular libraries cobbled together into something that works. Meaning most errors you'll encounter, others probably have too.

If you can't figure out anything, [Come see us on the unofficial Discord!](https://discord.gg/d4VkgSdNv6)