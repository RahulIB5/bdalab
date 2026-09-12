# Cybersecurity Practical 1 --- Complete Quick Reference

> **Environment:** Windows host → Oracle VirtualBox → Kali Linux\
> **Practical:** Packet Filtering & Analysis using Wireshark + Capturing
> Login Credentials over HTTP

------------------------------------------------------------------------

## 0. Overall Flow

``` text
Windows
  ↓
VirtualBox
  ↓
Kali Linux VM
  ↓
Update Kali + verify tools
  ↓
Part A — Wireshark packet capture
  ↓
Part B — Apache local login page
  ↓
Wireshark on loopback (lo)
  ↓
Capture HTTP POST
  ↓
Follow TCP Stream
  ↓
See credentials in plaintext
```

------------------------------------------------------------------------

# 1. Initial Setup --- Windows

## 1.1 Check virtualization

1.  Press `Ctrl + Shift + Esc`
2.  Open **Task Manager**
3.  Go to **Performance → CPU**
4.  Check:

``` text
Virtualization: Enabled
```

If disabled, enable Intel VT-x / AMD-V in BIOS/UEFI.

## 1.2 Install VirtualBox

Install **Oracle VirtualBox for Windows** using the default installation
settings.

## 1.3 Download Kali Linux VM

Download the pre-built **Kali Linux VirtualBox 64-bit** image.

The downloaded archive is typically `.7z`.

## 1.4 Extract Kali

Extract the archive using 7-Zip or another archive utility.

You should get files similar to:

``` text
Kali-Linux-xxxx.vbox
Kali-Linux-xxxx.vdi
```

## 1.5 Add Kali to VirtualBox

1.  Open VirtualBox.
2.  Select **Machine → Add** (or **Tools → Add**).
3.  Browse to the extracted Kali folder.
4.  Select the `.vbox` file.
5.  Click **Open**.

Kali should now appear in the VirtualBox VM list.

> **Important:** Once the VM is registered, start it from the VirtualBox
> Manager. Do not repeatedly open the `.vbox` file.

------------------------------------------------------------------------

# 2. Configure the Kali VM

Select the Kali VM → **Settings**.

Recommended settings used for this practical:

``` text
Memory:          4096 MB
Processors:      2 CPUs
Video Memory:    128 MB
```

## Network

For **Part A packet capture**, configure:

``` text
Adapter 1
Attached to:      Bridged Adapter
Adapter:          Your Wi-Fi/network adapter
Promiscuous Mode: Allow All
Virtual Cable:    Connected
```

Save the settings.

> If **Promiscuous Mode** is not visible, switch VirtualBox to
> **Expert** settings.

------------------------------------------------------------------------

# 3. Start and Prepare Kali Linux

1.  Start the Kali VM from VirtualBox.
2.  Log in.
3.  Open a terminal.

## Update Kali

Run:

``` bash
sudo apt update
```

Then:

``` bash
sudo apt upgrade -y
```

If asked whether services should be restarted automatically, select
**Yes**.

Wait until the upgrade finishes completely.

## Verify required tools

Run:

``` bash
wireshark --version
```

``` bash
burpsuite
```

``` bash
ettercap
```

``` bash
gcc --version
```

``` bash
gdb --version
```

Burp Suite can be closed after verifying that it launches.

------------------------------------------------------------------------

# PART A --- Packet Filtering and Analysis using Wireshark

## 4. Configure Network for Packet Capture

In VirtualBox:

**Kali VM → Settings → Network → Adapter 1**

Set:

``` text
Attached to:      Bridged Adapter
Promiscuous Mode: Allow All
Virtual Cable:    Connected
```

Save.

------------------------------------------------------------------------

# 5. Start Kali and Wireshark

Start Kali.

Open Terminal and run:

``` bash
sudo wireshark
```

Wireshark opens with the available network interfaces.

------------------------------------------------------------------------

# 6. Capture Traffic on `eth0`

Find:

``` text
eth0
```

Double-click `eth0` to begin packet capture.

Generate traffic while capturing. Examples:

-   Open websites in Firefox.
-   Ping a server.
-   Run `curl` commands.

------------------------------------------------------------------------

# 7. Apply Wireshark Display Filters

Use the display-filter bar at the top of Wireshark.

## DNS

``` text
dns
```

Shows DNS packets.

## HTTPS / TLS

``` text
tls
```

Shows TLS traffic.

## Specific IP

``` text
ip.addr == 18.161.216.37
```

Shows packets involving that IP address.

## TCP port 443

``` text
tcp.port == 443
```

Shows TCP traffic using port 443.

> **Tip:** After entering a filter, press `Enter`.

------------------------------------------------------------------------

# 8. Analyze Packets

Select a packet and expand the protocol sections.

## Ethernet Layer

Check:

``` text
Source MAC
Destination MAC
```

## IP Layer

Check:

``` text
Source IP
Destination IP
```

## TCP / UDP Layer

Check:

``` text
Source port
Destination port
Flags
```

Important TCP flags include:

``` text
SYN
ACK
```

## Application Layer

Depending on the packet, examine:

``` text
DNS query
HTTP request
TLS handshake
```

------------------------------------------------------------------------

# 9. Stop and Save Part A Capture

Click the **red square Stop** button.

Save the capture in `.pcap` format.

Recommended filename:

``` text
partA_capture.pcap
```

------------------------------------------------------------------------

# PART B --- Capturing Login Credentials over HTTP

## 10. Start Kali

Start the Kali VM from VirtualBox.

Open a terminal.

------------------------------------------------------------------------

# 11. Start Apache Web Server

Run:

``` bash
sudo service apache2 start
```

Create the test-login directory:

``` bash
sudo mkdir -p /var/www/html/testlogin
```

Enter the directory:

``` bash
cd /var/www/html/testlogin
```

------------------------------------------------------------------------

# 12. Create `login.html`

Run:

``` bash
sudo nano login.html
```

Paste:

``` html
<!DOCTYPE html>
<html>
<head>
 <title>Test Login Page</title>
</head>
<body>
 <h2>Login Form</h2>
 <form action="login.php" method="post">
  Username: <input type="text" name="username"><br><br>
  Password: <input type="password" name="password"><br><br>
  <input type="submit" value="Login">
 </form>
</body>
</html>
```

Save in nano:

``` text
Ctrl + O
Enter
Ctrl + X
```

------------------------------------------------------------------------

# 13. Create `login.php`

Run:

``` bash
sudo nano login.php
```

Paste:

``` php
<?php
$username = $_POST['username'];
$password = $_POST['password'];
echo "<h2>Login Attempt</h2>";
echo "Username: " . $username . "<br>";
echo "Password: " . $password . "<br>";
?>
```

Save:

``` text
Ctrl + O
Enter
Ctrl + X
```

------------------------------------------------------------------------

# 14. Check Apache

Run:

``` bash
sudo service apache2 status
```

You want Apache to show that it is running.

If necessary:

``` bash
sudo service apache2 start
```

------------------------------------------------------------------------

# 15. Open the Login Page

Open Firefox in Kali.

Go to:

``` text
http://127.0.0.1/testlogin/login.html
```

The **Test Login Page** should appear.

Alternative terminal test:

``` bash
curl http://127.0.0.1/testlogin/login.html
```

------------------------------------------------------------------------

# 16. Start Wireshark on Loopback

Open Wireshark:

``` bash
sudo wireshark
```

This time select:

``` text
lo
```

`lo` = loopback interface.

Double-click `lo` to start capturing.

------------------------------------------------------------------------

# 17. Filter for HTTP POST

In the Wireshark display-filter bar, enter exactly:

``` text
http.request.method == "POST"
```

Press `Enter`.

At this point, the packet list may remain blank until the login request
is generated. That is normal.

------------------------------------------------------------------------

# 18. Perform the Test Login

Switch to Firefox.

Use the test credentials:

``` text
Username: admin
Password: test123
```

Click:

``` text
Login
```

The PHP page should process the login attempt.

------------------------------------------------------------------------

# 19. Find the HTTP POST Packet

Return to Wireshark.

Because of the filter:

``` text
http.request.method == "POST"
```

the HTTP POST request should be visible.

Locate the POST packet.

------------------------------------------------------------------------

# 20. Follow the TCP Stream

Right-click the HTTP POST packet.

Select:

``` text
Follow → TCP Stream
```

A TCP Stream window opens.

The submitted login data should be visible in plaintext, including the
test credentials:

``` text
admin
test123
```

This is the main result of Part B.

------------------------------------------------------------------------

# 21. Stop and Save Part B Capture

Click the **red square Stop** button.

Save the capture as:

``` text
partB_capture.pcap
```

------------------------------------------------------------------------

# 22. Key Observations / Viva Points

## Part A

-   Network traffic can be captured in real time.
-   Wireshark display filters narrow the traffic to the protocol or
    packet of interest.
-   Ethernet information includes source/destination MAC addresses.
-   IP information includes source/destination IP addresses.
-   TCP/UDP information includes ports and TCP flags.
-   Application-layer information can show DNS queries, HTTP requests,
    or TLS handshakes.

## Part B

The HTTP POST request carries the login data in plaintext.

Therefore, when the HTTP traffic is captured and the TCP stream is
followed, the submitted credentials can be directly read.

### Why?

HTTP does not provide encryption for the application data.

HTTPS uses TLS to encrypt the traffic, making the credentials
unavailable as readable plaintext in the captured network packets.

------------------------------------------------------------------------

# 23. Files Produced

At the end of the practical, you should have:

``` text
partA_capture.pcap
partB_capture.pcap
```

The HTML/PHP files are located inside:

``` text
/var/www/html/testlogin/
```

Specifically:

``` text
/var/www/html/testlogin/login.html
/var/www/html/testlogin/login.php
```

------------------------------------------------------------------------

# 24. Final Shutdown

When everything is complete:

1.  Close Wireshark.
2.  Shut down / power off the Kali VM.
3.  Close the VirtualBox window.

------------------------------------------------------------------------

# 25. Ultra-Short Revision Checklist

## Setup

-   [ ] Enable virtualization
-   [ ] Install VirtualBox
-   [ ] Download Kali VirtualBox image
-   [ ] Extract `.vbox` + `.vdi`
-   [ ] Add `.vbox` to VirtualBox
-   [ ] Set RAM = 4096 MB
-   [ ] Set CPU = 2
-   [ ] Set Video Memory = 128 MB
-   [ ] Configure Bridged Adapter when required
-   [ ] Promiscuous Mode = Allow All
-   [ ] Virtual Cable = Connected
-   [ ] Start Kali
-   [ ] `sudo apt update`
-   [ ] `sudo apt upgrade -y`
-   [ ] Verify Wireshark/Burp/Ettercap/GCC/GDB

## Part A

-   [ ] `sudo wireshark`
-   [ ] Capture on `eth0`
-   [ ] Generate traffic
-   [ ] Filter `dns`
-   [ ] Filter `tls`
-   [ ] Filter `ip.addr == 18.161.216.37`
-   [ ] Filter `tcp.port == 443`
-   [ ] Analyze Ethernet/IP/TCP/UDP/Application layers
-   [ ] Stop capture
-   [ ] Save `partA_capture.pcap`

## Part B

-   [ ] `sudo service apache2 start`
-   [ ] `sudo mkdir -p /var/www/html/testlogin`
-   [ ] `cd /var/www/html/testlogin`
-   [ ] Create `login.html`
-   [ ] Create `login.php`
-   [ ] `sudo service apache2 status`
-   [ ] Open `http://127.0.0.1/testlogin/login.html`
-   [ ] `sudo wireshark`
-   [ ] Capture on `lo`
-   [ ] Filter `http.request.method == "POST"`
-   [ ] Username = `admin`
-   [ ] Password = `test123`
-   [ ] Click Login
-   [ ] Locate POST packet
-   [ ] Right-click → Follow → TCP Stream
-   [ ] Observe credentials in plaintext
-   [ ] Stop capture
-   [ ] Save `partB_capture.pcap`
-   [ ] Shut down Kali
-   [ ] Close VirtualBox

------------------------------------------------------------------------

## Practical Conclusion

**Part A:** Wireshark was used to capture, filter, and analyze network
packets.

**Part B:** A local HTTP login form was created and its POST request was
captured using Wireshark. Following the TCP stream showed that the
submitted credentials were transmitted in plaintext.

**Security takeaway:** Sensitive login data should be transmitted over
**HTTPS/TLS**, not plain HTTP.
