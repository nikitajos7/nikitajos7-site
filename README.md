# nikitajos7-site
CSE 135 Site

## Main Site
https://nikitajos7.site

Collector:
https://collector.nikitajos7.site

Reporting:
https://reporting.nikitajos7.site

---

Configured via:
- /etc/apache2/.htpasswd
- nikitajos7.site.conf

---

## Required Pages Verified

- Homepage with team + homework links  
  https://nikitajos7.site

- Team member page  
  https://nikitajos7.site/members/nikita.html

- favicon  
  /favicon.ico

- robots.txt  
  https://nikitajos7.site/robots.txt

- PHP verification  
  https://nikitajos7.site/hw1/hello.php

- Report page  
  https://nikitajos7.site/hw1/report.html

---


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

## Grader Login Instructions

The grader account is configured for SSH key authentication.

**Host:**  
nikitajos7.site (138.68.56.253)

**Username:**  
grader

**Authentication Method:**  
SSH key (ed25519)

**Private Key File:**  
grader_ed25519 (included in submission)

**Key Passphrase:**  
None

**Login Command:**
```bash
ssh -i grader_ed25519 grader@nikitajos7.site

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

## Analytics System: Matomo

I installed and configured **Matomo (self-hosted)** to track traffic and user activity on our website.

### Tool Used
- **Matomo** — https://matomo.org/

### Setup Summary
- Installed Matomo on the VM and served it at:
  https://analytics.nikitajos7.site
- Configured Nginx (reverse proxy) and Apache with PHP support.
- Completed the Matomo web installer (database, tables, superuser, site setup).
- Verified HTTPS access and dashboard functionality.

### Tracking Integration
The following tracking code was added to the `<head>` of `nikitajos7.site` to enable page view tracking:

```html
<script>
  var _paq = window._paq = window._paq || [];
  _paq.push(['trackPageView']);
  _paq.push(['enableLinkTracking']);
  (function() {
    var u="//analytics.nikitajos7.site/";
    _paq.push(['setTrackerUrl', u+'matomo.php']);
    _paq.push(['setSiteId', '1']);
    var d=document, g=d.createElement('script'), s=d.getElementsByTagName('script')[0];
    g.async=true; g.src=u+'matomo.js'; s.parentNode.insertBefore(g,s);
  })();
</script>
```

### Verification
- Matomo Dashboard: https://analytics.nikitajos7.site  
- Tracked Site: https://nikitajos7.site

## Server Information

- Server IP Address: 138.68.56.253
- Server OS: Ubuntu
- Web Server: Apache
- SSH Access: Key-based authentication
- SSH Username: nikita

## Homework 2 – CGI Programs

All Homework 2 demos are linked from the homepage under Homework 2 and are organized by language.

### Languages Used
- Perl (provided examples from Part 1)
- PHP
- NodeJS (proxied through Apache)

### Demo Programs Implemented (3 Languages × 5 Demos)
- hello-html-language
- hello-json-language
- environment-language
- echo-language
- state-language

Each demo is accessible via a direct link from the homepage and returns HTTP 200 responses.


## Third-Party Analytics – Free Choice

I chose Microsoft Clarity because it provides session replays and
heatmaps while remaining easy to integrate and free to use. Unlike
Google Analytics, Clarity focuses on visual interaction data such as
scroll depth and click behavior. Compared to LogRocket, it is less
invasive and does not require authentication to view sessions.

Overall, Clarity complemented Google Analytics and LogRocket by
providing a privacy-conscious, visual understanding of user behavior
on our site.
