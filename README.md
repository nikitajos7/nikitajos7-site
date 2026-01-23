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
