# 🚀 Running Nexus Nodes with Systemd (Beginner Friendly)

This guide shows you how to run your **Nexus nodes** on Ubuntu using **systemd** instead of `screen`.  
No coding required — just copy & paste the commands.  

---

## ✅ Why systemd?
- Nodes **auto-start** when your computer reboots  
- Easy to **start/stop/restart** nodes  
- Logs are stored and easy to check  
- Cleaner than managing `screen` sessions  

---

## 🛠 Prerequisites
- Ubuntu installed (WSL or native)  
- Nexus binary already installed at:  
  ```bash
  /home/<your-username>/.nexus/bin/nexus-network
  ```
- You have a registered **node ID**  

---

## 📝 Step 1: Find your username
Run:  
```bash
whoami
```
Example output:  
```
escanor
```
We’ll use this username in later steps.  

---

## 📝 Step 2: Create a systemd service file
Each node needs a service file. Let’s start with Node 1.  

Run:  
```bash
sudo nano /etc/systemd/system/nexus1.service
```

Paste this inside (replace `YOUR_USERNAME` and `YOUR_NODE_ID`):  

```ini
[Unit]
Description=Nexus Node 1
After=network.target

[Service]
User=YOUR_USERNAME
ExecStart=/usr/bin/screen -DmS nexus1 /home/YOUR_USERNAME/.nexus/bin/nexus-network start --node-id YOUR_NODE_ID
Restart=always
KillMode=control-group

[Install]
WantedBy=multi-user.target
```

Save and exit Nano:  
- Press **CTRL + O**, then **ENTER**  
- Press **CTRL + X** to exit  

---

## 📝 Step 3: Reload systemd
```bash
sudo systemctl daemon-reload
```

---

## 📝 Step 4: Start your node
```bash
sudo systemctl start nexus1
```

---

## 📝 Step 5: Enable auto-start on reboot
```bash
sudo systemctl enable nexus1
```

---

## 📝 Step 6: Check if it’s running
```bash
systemctl status nexus1
```

If successful, you’ll see:
```
Active: active (running)
```

---

## 📝 Step 7: Add more nodes
Repeat **Steps 2–6**, but change:  
- `nexus1` → `nexus2`, `nexus3`, etc.  
- Use the correct **node ID** for each.  

---

## 📝 Step 8: Check all nodes with a script
Create a helper script:  
```bash
nano check-nodes.sh
```

Paste this in:  
```bash
#!/bin/bash
echo "=== Nexus Node Status ==="
for i in {1..5}
do
    STATUS=$(systemctl is-active nexus$i)
    if [ "$STATUS" = "active" ]; then
        echo "Node $i: RUNNING"
    else
        echo "Node $i: STOPPED"
    fi
done
```

Save and exit, then make it executable:  
```bash
chmod +x check-nodes.sh
```

Run it:  
```bash
./check-nodes.sh
```

---

## 📖 Useful Commands
- Stop a node:  
  ```bash
  sudo systemctl stop nexus1
  ```
- Restart a node:  
  ```bash
  sudo systemctl restart nexus1
  ```
- View logs:  
  ```bash
  journalctl -u nexus1 -f
  ```

---

✨ That’s it! You’re running Nexus nodes with `systemd`.  

## 📖 Note
Nodes currently acting up, will get a fix soon
