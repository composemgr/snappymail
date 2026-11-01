## 👋 Welcome to snappymail 🚀

Simple, modern, lightweight and fast web-based email client

## 📋 Description

SnappyMail is a web-based email client, the maintained fork of RainLoop. It
speaks IMAP to your existing mail server, so it runs on its own here: a single
PHP-FPM and nginx container with no database and no mail ports of its own.
Mail accounts, domains and plugins are configured in the admin panel.

The image generates an admin password on first start. Read it out of the
data volume:

```bash
cat ./volumes/data/snappymail/_data_/_default_/admin_password.txt
```

Then open `http://172.17.0.1:64347/?admin` and log in as `admin`.

## 🚀 Services

- **app**: djmaze/snappymail:latest (container port 8888)

SnappyMail needs no infrastructure components. It stores its own
configuration and contacts in SQLite inside the data volume by default.

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/snappymail/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/snappymail" ~/.local/srv/docker/snappymail
cd ~/.local/srv/docker/snappymail
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install snappymail
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
SNAPPYMAIL_DEBUG=false
SNAPPYMAIL_UPLOAD_MAX_SIZE=25M
SNAPPYMAIL_MEMORY_LIMIT=128M
SNAPPYMAIL_SECURE_COOKIES=true
```

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64347` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Only the `app` service carries the proxy labels and joins the external
network. Keep `SNAPPYMAIL_SECURE_COOKIES` at `true` behind the proxy, the
login session then only works over HTTPS.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64347
- **Admin Panel**: http://172.17.0.1:64347/?admin

No mail protocol ports are published. SnappyMail is an IMAP and SMTP client,
not a server: your mail server's IMAP and SMTP ports must be reachable from
this container for mail to be read and sent.

## 📂 Volumes

- `./volumes/data/snappymail` - Configuration, SQLite database, contacts,
  admin password and uploaded avatars

## 🔐 Security

- The admin panel password is generated on first start and only exists in
  `admin_password.txt`, read it once then remove or secure that file
- `SNAPPYMAIL_SECURE_COOKIES=true` sends session cookies over HTTPS only, set
  it to `false` only for local plain HTTP testing
- SnappyMail stores your IMAP and SMTP credentials in its own database, back
  up `./volumes/data/snappymail` with the same care as the mail server
- Use HTTPS via the reverse proxy in production
- Regularly update Docker images for security patches
- Backup your data regularly

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart

# Read the generated admin password
cat ./volumes/data/snappymail/_data_/_default_/admin_password.txt
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+
- Network access to an existing IMAP and SMTP server

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
