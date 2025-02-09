# Local-Windows-Save-to-Docker-Dedicated-Palworld-Server

## 📄 Description

This guide aims to transfer a world and players to a dedicated Palworld server on Docker installed on Debian 12.

You will find all the commands, from Docker installation to the final configuration (world and players transferred).

⚠️ **Warning**: This method only works if you play with at least one friend, as you will need to join their guild at the end.

Of course, remember to change the default ports and paths if needed.

I want to clarify that I did not create anything; I simply adapted the work of others from GitHub and YouTube to suit my needs.

The sources of my research to create this guide are listed at the end. This guide was created in collaboration with [@Wolfy-creator](https://github.com/Wolfy-creator), thank you to him.

---

## 🔧 Minimum Requirements

- **Server**: Debian 12
- **CPU**: 2 cores, 4 threads
- **RAM**: 16 GB
- **Storage**: 20 GB
- **PC**: Windows 10 or 11
- **Administrator rights required**
- **Python installation**

---

## Step 1 :🛠️ Installation

### 1. Update the system:

```bash
apt update && apt upgrade -y
```

### 2. Install dependencies:

```bash
apt install -y apt-transport-https ca-certificates curl software-properties-common
```

### 3. Add Docker's GPG key:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 4. Add Docker repository to APT:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 5. Update packages:

```bash
apt update
```

### 6. Install Docker:

```bash
apt install -y docker-ce docker-ce-cli containerd.io
```

### 7. Verify Docker installation:

```bash
docker --version
```

### 8. Start and enable Docker on boot:

```bash
systemctl start docker
```

```bash
systemctl enable docker
```

### 9. Check Docker status:

```bash
systemctl status docker
```

### 10. Download Docker Compose:

```bash
curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### 11. Set Docker Compose permissions:

```bash
chmod +x /usr/local/bin/docker-compose
```

### 12. Verify Docker Compose installation:

```bash
docker-compose --version
```

---

## Installing the Palworld Server

### 13. Create the server directory:

```bash
mkdir -p /home/servers/palworld-server
```

```bash
cd /home/servers/palworld-server
```

### 14. Create the `docker-compose.yml` file:

```bash
nano docker-compose.yml
```

### 15. Add the following content to `docker-compose.yml`:

```yaml
version: '3.8'
services:
  palworld:
    image: thijsvanloef/palworld-server-docker:latest
    container_name: palworld-server
    ports:
      - "8211:8211/udp"
      - "27015:27015/udp"
    volumes:
      - ./data:/palworld
    environment:
      - PUID=1000
      - PGID=1000
      - PORT=8211
      - PLAYERS=16
      - MULTITHREADING=true
      - RCON_ENABLED=true
      - RCON_PORT=25575
      - RCON_PASSWORD=78952
    restart: unless-stopped
```

### 16. Start the server:

```bash
systemctl restart docker
```

```bash
docker-compose up -d
```

### 17. Get the server's IP address:

```bash
hostname -I
```

---

## 🛡️ Server Management

### Stop the Palworld server:

```bash
docker-compose down
```

### Start the Palworld server:

```bash
docker-compose up -d
```

⚠️ **Note**: Make sure you are in the `palworld-server` directory before executing the commands.

### Open the corresponding ports on your router.

### Connect to the Palworld server in-game at least once to properly load the files.

⚠️ **Warning**: The Palworld server may take some time to fully start (usually less than 1 minute).

------------------------------------------------------------
## Step 2 :🛠️ World transfer


### 1. To start the world transfer, switch off the palworld server::
```sh
docker-compose down
```

### 2. Navigate to the Save Directory:
```sh
cd /home/servers/palworld-server/data/Pal/Saved/SaveGames/0/#TheNumberOfActualServerGameSave
```

### 3. Replace Save Files:
Replace all existing files in this directory with the save files you want to transfer.

### 4. Start the Palworld Server:
```sh
docker-compose up -d
```

### 5. Verify in-game:
Connect to the game using any player **except the original host** to check if everything is working correctly.

----------------------------------------------------------------
## Step 3 :🛠️ Repair host


### 1. Restore the Original Host's Character:
1. Connect to the server with the **former host** to generate the player file.
2. Create a **new character** with any name and appearance, play for **2-3 minutes** to ensure the player is properly saved.
3. Disconnect from the server.
4. On the server, a new file in the `Players` folder should appear—identify the **GUID**.

### 2. Transfer the World Save to a Windows PC:
```sh
/home/servers/palworld-server/data/Pal/Saved/SaveGames/0/<YOUR_SAVE>/
```
1. Download and place the `uesave` folder at the root of **C:\**.
2. Move the save file (32-character name) into the `uesave` folder.
3. Install Python: [Download Python](https://www.python.org/downloads/).

### 3. Run the Fix Script:
1. Open **PowerShell as Administrator**.
2. Install `palworld-save-tools`:
```sh
python -m pip install palworld-save-tools==0.23.1
```
3. Update `pip`:
```sh
python.exe -m pip install --upgrade pip
```
4. Navigate to the `uesave` folder:
```sh
cd C:\uesave
```
5. Run the script to fix the host issue:
```sh
python fix_host_save.py C:\uesave\"YourGameSaveFolder" "New_GUID" "Old_GUID" True
```
6. Press **Enter** when you see the warning message: _"Warning: Running this script…"_

### 4. Restore the Fixed Save to the Server:
1. Replace the modified save file back onto the server.
2. In the `Players` folder, the old ID (`000…1.sav`) should disappear—this is normal as its data is transferred to the **New_GUID**.

### 4. Final Steps:
- Connect to the server with the **former host** and check if the character is restored.
- If you experience **guild issues**, leave your guild and rejoin a friend's guild (they must be online). Otherwise, you may lose access to your camp and chests.

🎉 **Enjoy your game!** 🎉

## Sources:
- [palworld-host-save-fix](https://github.com/xNul/palworld-host-save-fix)
- [palworld-save-tools](https://github.com/cheahjs/palworld-save-tools)
- [uesave-rs](https://github.com/trumank/uesave-rs)
- [YouTube Video Link](https://www.youtube.com/watch?v=mTLhWpM-4Do) : Special thanks to **@saliktor** for the helpful comment!

