# Program 3: Command Injection Attack

## Aim
To understand command injection using DVWA hosted on Metasploitable and observe how unvalidated input can cause extra system commands to execute.

## Requirements
- Oracle VirtualBox
- Kali Linux
- Metasploitable 2
- DVWA (Damn Vulnerable Web Application)
- Firefox

## Part A: Set up Metasploitable

### 1. Download and extract
1. On Windows, open the lab manual's download page: https://sourceforge.net/projects/metasploitable/
2. Download `Metasploitable2-Linux.zip`.
3. Right-click the ZIP file and select **Extract All**.
4. Locate `Metasploitable.vmdk` in the extracted folder.

### 2. Import the VM into VirtualBox
1. Open VirtualBox and click **New**.
2. Name: `Metasploitable`.
3. Leave the ISO image empty.
4. Select **Linux** and **Ubuntu (32-bit)** or **Other Linux (32-bit)**.
5. Under virtual hard disk, choose **Use an existing virtual hard disk file**.
6. Browse to and select `Metasploitable.vmdk`, then finish creating the VM.

## Part B: Configure the network

### 3. Set both VMs to the same network
1. Power off both VMs before changing network settings.
2. In VirtualBox, select **Metasploitable → Settings → Network → Adapter 1**.
3. Enable the adapter and select **Host-only Adapter**. Choose `VirtualBox Host-Only Ethernet Adapter` and ensure **Virtual Cable Connected** is checked.
4. Open **Kali Linux → Settings → Network → Adapter 1** and choose the same Host-only Adapter.
5. Save the settings.

> If the adapter controls are greyed out, make sure the VM is fully powered off (not running or in a saved state). If no host-only network is listed, open VirtualBox's Network Manager and check/create the Host-only Network.

## Part C: Run the experiment

### 4. Start Metasploitable and find its IP
1. Start Metasploitable.
2. Log in:
   - Username: `msfadmin`
   - Password: `msfadmin`
3. Run:
   ```bash
   ifconfig
   ```
4. Note the IP address shown for `eth0`. The manual's example is `192.168.56.102`, but your address may differ.

### 5. Start Kali and test connectivity
1. Start Kali Linux and log in.
2. In a Kali terminal, run:
   ```bash
   ping <Metasploitable-IP>
   ```
3. Continue when you receive replies. Use the actual IP from Step 4.

### 6. Open and configure DVWA
1. In Firefox on Kali, visit:
   ```text
   http://<Metasploitable-IP>/dvwa
   ```
2. Log in with the lab credentials:
   - Username: `admin`
   - Password: `password`
3. If prompted, click **Create/Reset Database**, then log in again.
4. Open **DVWA Security**, select **Low**, and click **Submit**.

### 7. Test command execution
1. Select **Command Execution** from the left menu.
2. Enter `127.0.0.1` and click **Submit**. Observe the ping output.
3. In this isolated lab, try each example separately:
   ```text
   127.0.0.1; ls
   127.0.0.1; pwd
   127.0.0.1; whoami
   127.0.0.1; ls -la
   127.0.0.1; ifconfig
   ```
4. Observe whether the page displays output from the additional command.

## Observation
The application runs the ping command and may also execute the command appended after `;`. This happens because user input is handled unsafely and becomes part of a system command.

## Result
Command injection was demonstrated in the intentionally vulnerable DVWA environment by observing additional command output after supplying crafted input.

## Prevention
- Avoid invoking an operating-system shell when a safer API is available.
- Validate input using a strict allowlist (for example, valid IP addresses only).
- Pass arguments safely instead of concatenating user input into a shell command.
- Run the application with least privilege.
- Perform regular security testing.

## Note
Use this exercise only in the isolated lab environment. The Metasploitable IP address may vary; use the address shown by `ifconfig`.
