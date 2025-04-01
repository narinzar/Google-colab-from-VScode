# Google Colab SSH Setup via Cloudflare and VS Code

## ⚠️ Disclaimer
This guide is the result of hours of research and testing. Use it at your own risk. While these steps should work, modifications to your system's SSH configuration and Cloudflare settings could have unintended consequences.

---

## Overview
This guide provides step-by-step instructions on setting up remote SSH access to Google Colab using Cloudflare Tunnel, configuring SSH on your local machine, and connecting via VS Code.

## Prerequisites
- A Google Colab account  
- Windows machine with PowerShell  
- VS Code installed  
- Git installed  

---
## Steps to Set Up SSH Access

### 1. Install and Launch SSH in Google Colab
Open a new Google Colab notebook and execute the following commands:

```python
!pip install colab_ssh --upgrade
from colab_ssh import launch_ssh_cloudflared
launch_ssh_cloudflared(password="colab")  # This will be your password for authentication within VS Code later
```

This will generate a Cloudflare Tunnel URL (e.g., `yesterday-locks-slides-upc.trycloudflare.com`). Copy this URL for later use.

📌 **[Open Colab with SSH Setup](https://colab.research.google.com/#create=true&snippet=true&code=%21pip%20install%20colab_ssh%20--upgrade%0Afrom%20colab_ssh%20import%20launch_ssh_cloudflared%0Alaunch_ssh_cloudflared%28password%3D%22colab%22%29)**  

### Important:

Before proceeding, make sure your Colab runtime is set to an **A100 GPU** for optimal performance.  

To check or change the runtime:  
- Click on **Runtime** in the Colab menu.  
- Select **Change runtime type**.  
- Choose **GPU (A100 preferred)** under **Hardware accelerator**.  

### 🛠️ Note: Steps 2 through 5 are only required the first time.
---
### 2. Install Cloudflare CLI on Windows
Run the following command in PowerShell:

```powershell
winget install Cloudflare.Cloudflared
```

---
### 3. Configure SSH on Your Local Machine
Open or create the SSH configuration file at:

```
C:\Users\narin\.ssh\config
```

Add the following lines:

```
Host yesterday-locks-slides-upc.trycloudflare.com
    HostName yesterday-locks-slides-upc.trycloudflare.com
    User root
    Port 22

Host *.trycloudflare.com
    HostName %h
    User root
    Port 22
    ProxyCommand C:\Users\narin\cloudflared-windows-amd64.exe access ssh --hostname %h
```

Replace `yesterday-locks-slides-upc.trycloudflare.com` with your Cloudflare Tunnel URL.

---
### 4. Connect to Google Colab via SSH
In PowerShell, run:

```powershell
ssh root@yesterday-locks-slides-upc.trycloudflare.com
```

Enter the password you set in Step 1 when prompted.

---
### 5. Connect to Google Colab from VS Code
1. Open **VS Code**.  
2. Press `Ctrl+Shift+P`, select **Remote-SSH: Connect to Host...**  
3. Enter:

   ```
   root@yesterday-locks-slides-upc.trycloudflare.com
   ```

   (Replace with your Cloudflare Tunnel URL from Step 1).  
4. Enter the password when prompted.  

---
### 6. Clone a GitHub Repository and Set Up a Virtual Environment
After connecting to the remote server, run the following commands in the terminal:

```sh
pip install virtualenv
virtualenv .venv
source .venv/bin/activate
```

---
### 7. Configure Git
Run the following commands to set your Git username and email:

```sh
git config --system user.name "GITHUB USERNAME"
git config --system user.email "YOUR EMAIL ADDRESS"
```

---
### 8. Test PyTorch and GPU Availability
To check if PyTorch is installed and whether a GPU is available, run the following Python script:

```python
import torch
print("PyTorch version:", torch.__version__)
print("Is CUDA available:", torch.cuda.is_available())
if torch.cuda.is_available():
    print("GPU Name:", torch.cuda.get_device_name(0))
```

#### Expected Output:
- If a GPU is available, you should see the CUDA-enabled device name.  
- If not, `torch.cuda.is_available()` will return `False`, meaning no GPU is detected.  

---
## Conclusion
Following these steps, you can now seamlessly connect to Google Colab via SSH, use VS Code for remote development, and manage your projects efficiently.  

📌 **[Open Colab with SSH Setup](https://colab.research.google.com/#create=true&snippet=true&code=%21pip%20install%20colab_ssh%20--upgrade%0Afrom%20colab_ssh%20import%20launch_ssh_cloudflared%0Alaunch_ssh_cloudflared%28password%3D%22colab%22%29)**  

Test your setup by running the PyTorch script to confirm GPU availability. 🚀
