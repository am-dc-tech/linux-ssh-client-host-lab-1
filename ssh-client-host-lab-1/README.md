# Lab: SSH from Windows Server 2025 to Linux Lite

## Summary

In this lab, I used two virtual machines, Windows Server 2025 and Linux Lite, to practice basic remote administration. I found the IP address of each machine, connected from Windows Server 2025 to Linux Lite using SSH, created a text file with the nano editor over that remote session, and then confirmed the file existed by locating it directly on the Linux Lite machine. This simulates a common help desk task: connecting to another system remotely to troubleshoot or make changes without sitting at it.

## Environment

- **VM 1:** Windows Server 2025 (SSH client)
- **VM 2:** Linux Lite (SSH server and target)
- **Network:** Both VMs on the same virtual network so they can reach each other
- **Tools:** `ipconfig`, `ip a`, `ssh`, `nano`, `ls`, `find`, `cat`

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

![SSH service running on Linux Lite](images/01-ssh-service-running.png)

> [!NOTE]
> Windows Server 2025 includes an SSH client, so nothing needs to be installed on the Windows side. The SSH server must be running on the target machine, otherwise the connection will be refused.

---

## Step 1: Find the IP Address of Windows Server 2025

1. On Windows Server 2025, open **PowerShell** or **Command Prompt**.
2. Run:

   ```
   ipconfig
   ```

3. Find the **IPv4 Address** under the active network adapter and write it down.

![ipconfig output on Windows Server 2025](images/02-windows-ipconfig.png)

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

![ip a output on Linux Lite](images/03-linux-ip.png)

## Step 3: Test the Connection

Before using SSH, check that Windows can reach Linux Lite.

1. On Windows Server 2025, in PowerShell, run:

   ```
   ping <linux-lite-ip>
   ```

2. Confirm you get replies.

![Successful ping to Linux Lite](images/04-ping.png)

> [!TIP]
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

![Successful SSH connection to Linux Lite](images/05-ssh-connected.png)

## Step 5: Create a Text File with nano

1. In the SSH session, run:

   ```bash
   nano labfile.txt
   ```

2. Type some text, for example: `This file was created from Windows Server 2025 over SSH.`
3. Press **Ctrl + O**, then **Enter** to save.
4. Press **Ctrl + X** to exit nano.

![Creating the file in nano](images/06-nano-file.png)

## Step 6: Confirm the File in the SSH Session

1. Still in the SSH session, run:

   ```bash
   ls
   ```

2. Confirm `labfile.txt` appears in the list.

![labfile.txt listed over SSH](images/07-ls-over-ssh.png)

## Step 7: Find the File on Linux Lite

Now check from the Linux Lite machine itself to prove the file was saved there and not on Windows.

1. Switch to the **Linux Lite VM** and open a terminal.
2. In the home directory, run:

   ```bash
   ls
   ```

   or search for it:

   ```bash
   find ~ -name labfile.txt
   ```

3. Open it to check the contents:

   ```bash
   cat labfile.txt
   ```

4. Confirm the text matches what you typed over SSH.

![labfile.txt found on Linux Lite](images/08-file-on-linux.png)

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

## Troubleshooting Notes

| Problem | Likely cause | Fix |
|---|---|---|
| `Connection refused` | SSH server not installed or not running on Linux Lite | Install `openssh-server` and run `sudo systemctl enable --now ssh` |
| `Connection timed out` | VMs on different networks, wrong IP, or a firewall | Check the virtual network setting, confirm the IP, and check the firewall |
| `Permission denied` | Wrong username or password | Confirm the Linux Lite username and password |
| `ssh` not recognized | OpenSSH client not available on Windows | Enable the OpenSSH Client optional feature |

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

