---
layout: default
title: Running Pygmalion on Windows
nav_order: 1
description: "How to configure Pygmalion to run directly on your own hardware. This guide should hopefully be noob-proof."
---
# Running Pygmalion on Windows (mostly)

So, you think you've got what it takes to run state-of-the-art conversational AI on your own hardware? Let's put that to the test!

## System requirements

### **Pygmalion is very heavy, it will eat _13.5GB of VRAM, and up to 18GB_ at the maximum context size of 2048.** There are only a handful of graphics and accelerator cards which can support running the model properly.
<br>

> The 350m, 1.3B and 2.7B model are not listed here as they are sorely out-of-date and will most likely not give satisfying results. You're free to give them a try, but anything besides 2.7B will quickly become incoherent.

> All 11-12GB RTX cards will be added this list once KoboldAI gains user-friendly support for loading models in 8-bit.

<br>

### Supported NVIDIA graphics cards
- GeForce RTX 3090 (24GB)
- GeForce RTX 3090 TI (24GB)
- GeForce RTX 4090 (24GB)
- GeForce RTX 4080 (16GB) *you will need to set context size to 1024!
- Tesla M40 (24GB)
- Tesla P40 (24GB)
- Quadro P6000 (24GB)
- Tesla P100 (16GB) *you will need to set context size to 1024!

> Tesla cards do not have a fan! And require a FULL PCI Express 3.0 16x bus, as well as your motherboard BIOS supporting Above 4G Decoding! (Most professional workstation style PCs, expecially from Dell have a tendency to block out Tesla cards entirely in firmware.)

### Supported AMD graphics cards:
#### These will _only work on Linux_, because the "ROCm" AI runtime is not available for Mac or Windows.
- Radeon RX 6800 (16GB) *you will need to set context size to 1024!
- Radeon RX 6800XT (16GB) *you will need to set context size to 1024!
- Radeon RX 6900XT (16GB) *you will need to set context size to 1024!
- Radeon RX 6950XT (16GB) *you will need to set context size to 1024!
- Radeon RX 7900XT (20GB)
- Radeon RX 7900XTX (24GB)
- Instinct MI50 (16GB) *you will need to set context size to 1024!

> More powerful cards exist in the enterprise space, but if you have one of those, why are you even reading this?

### Supported Intel graphics cards:
![ragequit](https://media.tenor.com/hywZNm_1efkAAAAd/csgo-banging-table.gif)


## Glossary
<details>
    <summary>Click to expand</summary>
    
    This section is intended to brief you on what all the technical jargon in this document means.

    CUDA:
    A library created by NVIDIA to use compute on their GPUs. Anytime you see CUDA mentioned, assume it only applies to NVIDIA.  

    ROCm:  
    Similar to CUDA but for AMD cards. ROCm has less support than CUDA, as it's mostly made for professional enterprise cards, but some consumer cards are supported. Google is your friend on this one.  

    Kobold or KAI: 
    KoboldAI is an application and runtime to load language models easily.  

    TavernAI: 
    A graphical application made specifically to have chats using language models. It can run locally or using a remote server.  

    VRAM:
    Video Memory is the RAM on your graphics card. It is much faster and higher bandwidth than the system RAM on your motherboard that connects to the CPU.
    > In Windows Task Manager, you can go to the Performance tab, then click on your graphics card on the left. **Only the "Dedicated Video Memory" section matters.** The other values aren't useful, and do not represent any real amount of memory you may have.

</details>

<br>

## I got one of the funny graphics cards, how do?
Let's get on with the actual tutorial. These instructions are for Windows. Please refer to [the Linux instructions](pygmalion-linux#installing-dependencies) for AMD cards.

### Installing KoboldAI:
- [Download KoboldAI](https://koboldai.org/windows) and run the installer.  
  Make sure you don't have a `B:` drive as that will be used for the installation.  
  **When you reach the updater script, PICK OPTION 2.**
<!-- - Extract the ZIP to a drive you have at least 25GB of free space on. Using an SSD will speed up startup time for the model a lot.   -->
<!-- **Use a folder name without spaces at the root of the drive, e.g. `E:\KoboldAI`** -->
  <!-- > If you know your way around git, then just git clone, it'll make updates easier in the future too. -->
<!-- - In the extracted folder, run the `install_requirements.bat` file by double-clicking it. **Though it's recommended to right-click it and Run As Administrator.** Type `2` and press Enter. Then let it do it's thing. -->
<!-- - When it says `Press any key to continue.` then you're done installing Kobold. -->

### Starting KoboldAI
Start Kobold from the Start Menu shortcut. **Do NOT run as administrator!**

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
- Run `start.bat` **Do NOT run as administrator!**

### Using Tavern and connecting it to KoboldAI
- Once you run `start.bat` the NodeJS server will start, and launch Tavern in your web browser.
- You're now in Tavern! but it's not connected.
- Click the hamburger menu (three lines) in the top-right of the page
- Click on Settings
- In the API url section, put in `http://127.0.0.1:5000/api`
- Click Connect.
  > You'll have to manually click connect each time you close and reopen or refresh the TavernAI tab.
- You can now close out of settings and use Tavern! Or you can go to Characters, and create characters.

### A quick recap for the next time!
- Go to your KoboldAI folder, run `play.bat` **Do NOT run as administrator!**
- Click `AI` then `Chat models`, then the model you wish to load, then finally click `Load`
- Wait for it to load.
- Kobold can now be left alone.
- Go to your TavernAI folder, run `start.bat` **Do NOT run as administrator!**
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

## Reducing context size for 16GB cards
- In Tavern click on "Settings"
- Scroll down to "Pro Settings"
- Set the "Context Size" to "1024 Tokens"

## Something broke!
First off, Google is your friend, most of Kobold and Tavern are really just a hodge-podge of existing popular libraries cobbled together into something that works. Meaning most errors you'll encounter, others probably have too.

If you can't figure out anything, [Come see us on the official Discord!](https://discord.com/invite/bv4xczmcyk)
