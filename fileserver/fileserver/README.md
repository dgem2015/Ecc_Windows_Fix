# WindowsFix File Server

A minimal nginx Docker container that serves `WindowsFix.zip` (and any other
files you drop into `files/`) over HTTP.

## Directory layout

```
fileserver/
├── Dockerfile
├── docker-compose.yml
├── nginx.conf
├── README.md
└── files/
    └── WindowsFix.zip   ← the file being served
```

---

## Quick start (on your VPS)

### 1. Copy this folder to the VPS

```bash
# From your local machine:
scp -r fileserver/ user@YOUR_VPS_IP:~/fileserver
```

Or clone / upload however you prefer (SFTP, rsync, etc.)

### 2. SSH into the VPS and build + run

```bash
cd ~/fileserver
docker compose up -d --build
```

That's it. The container starts automatically on reboot (`restart: unless-stopped`).

### 3. Download the file

```
http://YOUR_VPS_IP/files/WindowsFix.zip
```

The root URL (`http://YOUR_VPS_IP/files/`) also shows a directory listing of
everything in the `files/` folder.

---

## Adding or updating files

Because `files/` is mounted as a volume, just drop new files into the folder
on the host — no rebuild needed:

```bash
cp NewFile.zip ~/fileserver/files/
```

They appear at `http://YOUR_VPS_IP/files/NewFile.zip` immediately.

---

## Changing the port

If port 80 is already in use on your VPS, edit `docker-compose.yml`:

```yaml
ports:
  - "8080:80"   # serve on port 8080 instead
```

Then the URL becomes `http://YOUR_VPS_IP:8080/files/WindowsFix.zip`.

---

## Useful commands

| Command | What it does |
|---------|-------------|
| `docker compose up -d --build` | Build image and start in background |
| `docker compose down` | Stop and remove the container |
| `docker compose logs -f` | Tail live access/error logs |
| `docker compose restart` | Restart the container |
| `docker ps` | Confirm container is running |

---

## Firewall (if downloads aren't reachable)

Make sure port 80 (or whichever port you chose) is open on your VPS:

```bash
# UFW (Ubuntu/Debian)
sudo ufw allow 80/tcp

# firewalld (CentOS/RHEL)
sudo firewall-cmd --permanent --add-port=80/tcp && sudo firewall-cmd --reload

# iptables
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

Most VPS providers also have a **Security Group** or **Firewall** panel in
their web console — make sure inbound TCP 80 is allowed there too.

---

## Optional: HTTPS with Let's Encrypt (certbot)

If you have a domain pointed at the VPS, you can add a free SSL cert:

```bash
# Install certbot on the host (not inside Docker)
sudo apt install certbot
sudo certbot certonly --standalone -d yourdomain.com

# Then update nginx.conf and docker-compose.yml to mount the certs and
# listen on 443. See certbot docs for the full nginx config snippet.
```
