# nikitajos7-site
CSE 135 Site

## GitHub Auto-Deploy Setup

This site is deployed automatically to a DigitalOcean droplet on every push to `main` using GitHub Actions.

### How it works
- Source files live in the `site/` directory in this repo.
- A GitHub Actions workflow (`.github/workflows/deploy.yml`) runs on every push to `main`.
- The workflow uses SSH + `rsync` to copy `site/` to the Apache DocumentRoot:
  `/var/www/nikitajos7.site/html/`

### GitHub Secrets
The workflow uses these GitHub Actions secrets:
- `DO_HOST` = droplet IP address
- `DO_USER` = SSH username (nikita)
- `DO_SSH_PRIVATE_KEY` = SSH private ke

### Manual deploy
No manual steps are required; pushes to `main` deploy automatically.

## Team Site Authentication

The team site is protected using Apache Basic Authentication.

Username: teamuser  
Password: password123

Auth is configured in Apache using:
- /etc/apache2/.htpasswd
- <Directory> block in nikitajos7.site.conf


## HTTP Compression (mod_deflate)

After enabling Apache mod_deflate, HTML, CSS, and JavaScript files are now served using gzip compression. In Chrome DevTools, the Network tab shows that index.html is returned with the header Content-Encoding: gzip. The “Transferred” size is significantly smaller than the original resource size, indicating that the file is being compressed before being sent over the network. DevTools also confirms that the browser automatically decompresses the content for rendering, so the page appears unchanged visually, but uses less bandwidth and loads more efficiently.

## Step 6 – Obscure Server Identity

To change the Server header to `CSE135 Server`, I configured NGINX as a reverse proxy in front of Apache. Apache was originally serving HTTPS directly. Although setting `ServerTokens Prod` and `ServerSignature Off` removed version numbers, Apache still returned `Server: Apache`. I then realized that it wouldn't be that simple, so to fully control the Server header, I installed NGINX and moved Apache to listen only on an internal port (8080). NGINX now handles all public HTTP and HTTPS traffic and proxies requests to Apache. 

In the NGINX configuration, I hid Apache’s Server header and added a custom Server header using:

- `proxy_hide_header Server;`
- `more_set_headers "Server: CSE135 Server";`
- `server_tokens off;`

This makes sure that external clients only see:

```
Server: CSE135 Server
```

and no Apache or NGINX version information is exposed.
