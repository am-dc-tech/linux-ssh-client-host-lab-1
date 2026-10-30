# Lab: SSH from Windows Server 2025 to Linux Lite

## Summary

In this lab, I used two virtual machines, Windows Server 2025 and Linux Lite, to practice basic remote administration. I found the IP address of each machine, connected from Windows Server 2025 to Linux Lite using SSH, created a text file with the nano editor over that remote session, and then confirmed the file existed by locating it directly on the Linux Lite machine. This simulates a common help desk task: connecting to another system remotely to troubleshoot or make changes without sitting at it.

## Environment

- **Linux User**: oem
- Windows User: AMTECH/Administrator
- **VM 1:** Windows Server 2025 (SSH client)
- **VM 2:** Linux Lite (SSH server and target)
- **Network:** Both VMs on the same virtual network so they can reach each other


## Lab Outline

1. Set up the lab: confirm both VMs can reach each other and the SSH server is running on Linux Lite
2. Find the IP address of Windows Server 2025
3. Find the IP address of Linux Lite
4. Connect from Windows Server 2025 to Linux Lite using SSH
5. Create a text file with nano over the SSH session
6. Find the file on Linux Lite
7. Verify the contents

---

## Prep: Set Up the Lab

1. Start both VMs.
2. Make sure both VMs use the same virtual network in your hypervisor (for example, Bridged, NAT Network, or Internal Network). If they are on different networks, they will not reach each other.
3. On Linux Lite, install and start the SSH server if it is not already running:

   ```bash
   sudo apt update
   sudo apt install openssh-server
   sudo systemctl enable --now ssh
   ```

4. Confirm the SSH service is running:

   ```bash
   sudo systemctl status ssh
   ```

   Look for `active (running)`. Press `q` to exit the status screen.

<img width="795" height="614" alt="1 install-ssh-linux" src="https://github.com/user-attachments/assets/6d30e33f-330b-4b0d-ab63-0e12900b1c65" />


5. Windows SSH
   
> Windows Server 2025 includes an SSH client, so nothing needs to be installed on the Windows side. The SSH server must be running on the target machine, otherwise the connection will be refused.

---

## Step 1: Find the IP Address of Windows Server 2025

1. On Windows Server 2025, open **PowerShell** or **Command Prompt**.
2. Run:

   ```command prompt
   ipconfig
   ```
3. Find the **IPv4 Address** under the active network adapter and write it down.

<img width="615" height="238" alt="2 ipconfig" src="https://github.com/user-attachments/assets/484002a4-5e22-4497-a20f-3e51e29df099" />

## Step 2: Find the IP Address of Linux Lite

1. On Linux Lite, open a terminal.
2. Run:

   ```bash
   ip a
   ```

   or:

   ```bash
   hostname -I
   ```

3. Find the IPv4 address on the main interface (often `eth0` or `enp0s3`) and write it down.

<img width="895" height="316" alt="3 linus-lite-ip" src="https://github.com/user-attachments/assets/59ac238c-71fd-4dc3-beb7-fc5afc0a0013" />

## Step 3: Test the Connection

Before using SSH, check that Windows can reach Linux Lite.

1. On Windows Server 2025, in PowerShell, run:

   ```
   ping <linux-lite-ip>
   ```

2. Confirm you get replies.

<img width="512" height="106" alt="4 window-ping-to-linux" src="https://github.com/user-attachments/assets/e496bdd9-e3f7-4ff7-85dd-6ffa1b1bf2d4" />


> If the ping fails, check that both VMs are on the same virtual network and that the IP address is correct. A firewall can also block ping replies, so a failed ping is not always a network problem. Try the SSH connection anyway.

## Step 4: Connect with SSH

1. On Windows Server 2025, in PowerShell, run:

   ```
   ssh <username>@<linux-lite-ip>
   ```

   Example: `ssh student@192.168.1.50`

2. The first time you connect, you will be asked to trust the host fingerprint. Type `yes` and press Enter.
3. Enter the Linux Lite user's password when prompted.
4. The prompt changes to the Linux Lite shell, which confirms the connection worked.

<img width="564" height="174" alt="ssh-success-win-to-lin" src="https://github.com/user-attachments/assets/9f394b20-5151-4cea-8647-ffab051265a2" />


## Step 5: Create a Text File with nano

1. In the SSH session, run:

   ```bash
   nano sshlabfile.txt
   ```

2. Type some text, for example: `This file was created from Windows Server 2025 over SSH.`
3. Press **Ctrl + O**, then **Enter** to save.
4. Press **Ctrl + X** to exit nano.

<img width="524" height="153" alt="creating-nano-file" src="https://github.com/user-attachments/assets/c5696e83-c763-4727-82d5-cf2c358ef419" />


## Step 6: Confirm the File in the SSH Session

1. Still in the SSH session, run:

   ```bash
   ls
   ```

2. Confirm `sshlabfile.txt` appears in the list.

<img width="813" height="114" alt="confirm-txt-file-exist" src="https://github.com/user-attachments/assets/9457dcc8-0052-488a-8f2d-414476448c1c" />



## Step 7: Find the File on Linux Lite

Now check from the Linux Lite machine itself to prove the file was saved there and not on Windows.

1. Switch to the **Linux Lite VM** and open a terminal.
2. In the home directory, run:

   ```bash
   ls
   ```

3. Open it to check the contents:

   ```bash
   cat labfile.txt
   ```

4. Confirm the text matches what you typed over SSH.


<img width="644" height="249" alt="cat-sshlabfile" src="https://github.com/user-attachments/assets/7aef636f-b7f1-45ec-b330-0cbbe1dcba5e" />

## Step 8: Close the Connection

1. Switch back to Windows Server 2025.
2. In the SSH session, type:

   ```bash
   exit
   ```

3. The prompt returns to PowerShell on Windows.

---

## Final Result

| Item | Result |
|---|---|
| Windows Server 2025 IP | Found with `ipconfig` |
| Linux Lite IP | Found with `ip a` |
| SSH connection | Windows Server 2025 to Linux Lite succeeded |
| File created | `labfile.txt` created with nano over SSH |
| File located | Found on Linux Lite with `ls` and `find` |
| Contents verified | Matched what was typed over SSH |

---

## What I Learned

- How to find IP addresses on both Windows (`ipconfig`) and Linux (`ip a`), which is the first step in almost any remote support task.
- How SSH lets me control one machine from another through a command-line session, and that Windows has a built-in SSH client.
- That the SSH server must be installed and running on the target before a connection works.
- How to create, save, and exit a file in nano.
- That files created in a remote session are saved on the remote machine, not the one I am typing from, and that I can prove it by finding the file on Linux Lite.
- Basic Linux navigation and file searching with `ls`, `find`, and `cat`.
- That remote access lets a help desk technician fix issues on machines in other locations without being physically there.

## Tools Used

Windows Server 2025, Linux Lite, OpenSSH, PowerShell, nano, `ipconfig`, `ip a`, `ping`, `ls`, `find`, `cat`

