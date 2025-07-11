# VIT-Vellore Wi-Fi Automation for macOS

Easily automate login to VIT Wi-Fi on macOS using [wifiwatcher](https://github.com/RamanaRaj7/wifiwatcher). This guide walks you through installing wifiwatcher, creating a simple auto-login script, and configuring it to execute automatically every time you connect to a VIT Wi-Fi network. I have also included a `manual one click login using shortcut` at the bottom.

If you have any doubts while implementing this, please create an issue.
---

## Prerequisites

- macOS
- [Homebrew](https://brew.sh/)

---

## Installation Steps

### 0. First disable captive portal by running this command 

```bash
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.captive.control Active -boolean false
```
you can enable it if you want using 

```bash
sudo defaults delete /Library/Preferences/SystemConfiguration/com.apple.captive.control Active
```
now you have to restart the computer to see its effect 

### 1. Install wifiwatcher

Install wifiwatcher using Homebrew:

```bash
brew install ramanaraj7/tap/wifiwatcher
```

---

### 2. Initial Setup

Generate the default configuration file:

```bash
wifiwatcher --setup
```
This creates `~/.wifiwatcher`.

---

### 3. Create the VIT Login Script

Create your script directory and open a new automation script file:

```bash
mkdir -p ~/path/to/your/scripts
nano ~/path/to/your/scripts/wifi-automation.sh
```

Paste this script, then fill in your VIT credentials at the top:

```bash
#!/bin/bash

# =========================
#   SET YOUR CREDENTIALS
# =========================
ID="YOUR_ID_HERE"
PASSWORD="YOUR_PASSWORD_HERE"
# =========================

echo "Attempting to authenticate..."

response=$(curl -s --data-urlencode "userId=$ID" \
                 --data-urlencode "password=$PASSWORD" \
                 --data-urlencode "serviceName=ProntoAuthentication" \
                 "http://phc.prontonetworks.com/cgi-bin/authlogin?URI=http://www.msftconnecttest.com/redirect")

plain_response=$(echo "$response" | sed 's/<[^>]*>//g')

if echo "$plain_response" | grep -q "Congratulations"; then
  echo "Authentication successful."
else
  echo "Authentication failed."
fi
```

To save and exit:  
`Ctrl+X`, then `Y` and `Enter`.

Make your script executable:

```bash
chmod +x ~/path/to/your/scripts/wifi-automation.sh
```

---

### 4. Configure wifiwatcher

Open your wifiwatcher configuration:

```bash
nano ~/.wifiwatcher
```

At the end of the file, add this line (replace with your actual username and script path):

```
/Users/your-username/path/to/your/scripts/wifi-automation.sh {wificontain:VIT}
```

To save and exit:  
`Ctrl+X`, then `Y` and `Enter`.

**Tip:**  
- This will run your script automatically whenever you connect to any Wi-Fi network with "VIT" in its name.

---

### 5. Start the Service

Start wifiwatcher as a background service:

```bash
brew services start wifiwatcher
```

To check if it's running:

```bash
brew services info wifiwatcher
```

Now, whenever you connect to VIT Wi-Fi, your automation script will run automatically!

---

## Testing & Debugging

You can manually test and debug wifiwatcher with:

```bash
wifiwatcher --monitor
```

---

## Uninstall / Stop Service

To stop wifiwatcher:

```bash
brew services stop wifiwatcher
```

To remove wifiwatcher:

```bash
brew uninstall wifiwatcher
```

---

## Notes & Customization

- **Script Flexibility:**  
  This is a simple script, but you can extend it to suit your needs (notifications, logging, etc.).

- **Preventing Timeout:**  
  If your Wi-Fi connection times out, you can run a ping command in the background to keep it alive.  
  **Important:** If you do this, ensure you kill the ping process on disconnect to avoid multiple background processes.  
  You can use a cleanup script and add it to your `~/.wifiwatcher` config, for example:
  ```
  /Users/your-username/path/to/your/scripts/cleanup.sh {on:disconnect} {wificontain:VIT}
  ```

- **Explore More:**  
  For advanced triggers and configuration options, check out the [wifiwatcher](https://github.com/RamanaRaj7/wifiwatcher) repository.

- **Security Warning:**  
  Your credentials are stored in plain text in the automation script.  
  **Never share your script or configuration file!**

---

## Manual Login with Shortcuts (Alternative Method)

If you prefer a manual approach or want a quick-access button, you can use the macOS/iOS Shortcuts app:

1. Add the shortcut using this link:  
   [VIT Wi-Fi Login Shortcut](https://www.icloud.com/shortcuts/0ccd0016a8ec4404a563e8501b7950c2)
<div align="center">
 <img width="532" alt="Screenshot 2025-07-10 at 12 51 12 AM" src="https://github.com/user-attachments/assets/c329366a-8fa0-48cd-a22c-1a5dcd7e61cd" />
</div>
2. After adding, **edit the shortcut** by double clicking on the shortcut added and enter your VIT-wifi login ID and password in the respective fields.
<img width="1112" alt="Screenshot 2025-07-10 at 12 48 15 AM" src="https://github.com/user-attachments/assets/51d7d754-768d-4a0c-9c7a-965a2164116a" />
click on "Open Preferences"
<img width="1312" height="838" alt="Screenshot 2025-07-10 at 11 06 57 PM" src="https://github.com/user-attachments/assets/47e03a90-0565-4fe7-b778-89aa746c08f6" />

<div align="center">toggle on "Allow Running Scripts"
 <img width="432" height="384" alt="Screenshot 2025-07-10 at 11 07 20 PM" src="https://github.com/user-attachments/assets/2d698132-29e2-45ef-8ebf-ab953275e8f7" />
</div>
now replace YOUR_ID_HERE with your user id and YOUR_PASSWORD_HERE with your password
<img width="1312" alt="Screenshot 2025-07-10 at 12 39 51 AM" src="https://github.com/user-attachments/assets/f809483f-e585-47d9-aae5-008c8054cad0" />


3. Add this shortcut to your Dock or Widgets for quick access.  
   This is useful when you want to trigger the login manually with a single tap/click by clicking on the widget.
<img width="1412" alt="Screenshot 2025-07-10 at 1 13 44 AM" src="https://github.com/user-attachments/assets/ef125cbf-ea41-41ce-b859-0e8db99bcf21" />

<div align="center">
  <img width="408" alt="Screenshot 2025-07-10 at 12 45 09 AM" src="https://github.com/user-attachments/assets/834cb9d7-4987-481b-b1dd-b19e548370f1" />
</div>

   https://github.com/user-attachments/assets/ca8d43ee-1add-41bd-a3b3-a5da9e1c1df1



