# Local-Windows-Save-to-Docker-Dedicated-Palworld-Server

## 📄 Description

This guide aims to transfer a world and players to a dedicated Palworld server on Docker installed on Debian 12.

You will find all the commands, from Docker installation to the final configuration (world and players transferred).

⚠️ **Warning**: This method only works if you play with at least one friend, as you will need to join their guild at the end.

Of course, remember to change the default ports and paths if needed.

I want to clarify that I did not create anything; I simply adapted the work of others from GitHub and YouTube to suit my needs.

The sources of my research to create this guide are listed at the end. This guide was created in collaboration with **@Wolfblackry**, thank you to him.

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

## 🛠️ Installation

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

## 🛠️ Installing the Palworld Server

### 1. Create the server directory:

```bash
mkdir -p /home/servers/palworld-server
cd /home/servers/palworld-server
```

### 2. Create the `docker-compose.yml` file:

```bash
nano docker-compose.yml
```

### 3. Add the following content to `docker-compose.yml`:

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

### 4. Start the server:

```bash
systemctl restart docker
docker-compose up -d
```

### 5. Get the server's IP address:

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

---

Do you want to add a specific section or improvement? 🚀

