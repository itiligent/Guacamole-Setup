# Guacamole 1.5.2 Virtual Desktop/Jump Server Appliance

A menu based build & install script for Guacamole 1.5.2 with support for SSL reverse proxy, AD integration, Multi-Factor Authentication and further security hardening.

## Automatic Build, Install & Config Script

To install Guacamole, copy and paste the following command into your terminal:

```
wget https://raw.githubusercontent.com/itiligent/Guacamole-Setup/main/1-setup.sh && chmod +x 1-setup.sh && ./1-setup.sh
```

## Prerequisites

- Any recent flavors of Ubuntu / Debian / Raspbian 
 ### PLEASE NOTE: LASTEST DEBIAN 12 HAS SEVERAL PENDING ISSUES - SEE ISSUE #8
- Minimum 8GB RAM and 40GB HDD
- Public or private DNS entries that match the default physical interface IP address (required for self SSL)
- Incoming access on TCP ports 22, 80, and 443

## Setup Menu Flow

### 1. Setup MYSQL

Install Guacamole with a new local MYSQL, or use an existing/remote MySQL instance. 

- Sub option: Add MySQL `mysql_secure_installation` settings to the local or remote MySQL instance

### 2. Select Authentication Extension

Choose an authentication extension [DUO, TOTP, LDAP or None]  - simultaneous TOTP and DUO not possible, but LDAP with TOTP is ok.)

### 3. Add Optional Reverse Proxy Front End to Guacamole

- Install Nginx Reverse Proxy?: y/n (N = default Guacamole frontend `http://hostname:8080/guacamole`)
- Install Nginx with no SSL?: y/n (Y = `http://hostname.local`)
- Install Nginx with self-signed SSL certificates?: y/n (Y = `https://hostname.local`. *This option generates Windows and Linux browser certificates (`$site.crt`, `$site.key`, and `$site.pfx`) in the `$DOWNLOAD_DIR/guac-setup` directory and configures SSL with Nginx.)*
- Install Nginx with Let's Encrypt certificates?: y/n (Y =`https://public.site.com`. *This configures Nginx with a newly created LetsEncrypt certificate and sets up auto renewals.)*

### Optional post install hardening

The installer downloads additional scripts for:
- Adding a fail2ban lockdown policy for Guacamole
- Encrypting internal traffic between the Guacamole client and Guacd daemon with SSL
- Integrating with Active Directory (See ACTIVE-DIRECTORY-HOW-TO.md) 
- Adding email alerts via Microsoft365 (SMTP auth)

## Installation Notes

To create an unattended setup or further customise the setup script, follow these steps:
1. Paste and run the wget link above.
2. Exit `1-setup.sh` script at the first prompt (at this point, only the scripts are downloaded).
3. Edit the "Silent setup options" section of `1-setup.sh`. *Variables with an actual setting (e.g., `VARIABLE="value"`) will not prompt during the interactive setup. With the right combination of variable inputs, it is possible to mass deploy Guacamole, Nginx, and SSL with zero touch.*
4. After making any script edits, ensure that you run the setup script saved locally with `./1-setup.sh` instead of using the web link. *To keep your script adaptations and avoid overwriting, comment out the relevant wget lines in the "Download GitHub Setup" section at the top of `1-setup.sh` before running setup locally. Commented out scripts will not be re-downloaded, and the installer will work from the local customised copy.*
5. Do _*NOT*_ run `./1-setup.sh` as sudo, it prompts as needed.
6. There should be no need to customise any scripts other than `1-setup.sh`. Be aware that all optional (manually run) "add-x.sh" scripts are dynamically updated during the installation with the exact variables you selected at install. Editing anything other than `1-setup.sh` may break this functionality, so make changes only if you understand the impacts.

### Glossary of items downloaded by the setup script

The setup command mentioned above downloads the following items into the `$DOWNLOAD_DIR/guac-setup` directory:

- `1-setup.sh`: The parent install script itself
- `2-install-guacamole.sh`: Guacamole installation script (inspired by [MysticRyuujin/guac-install](https://github.com/MysticRyuujin/guac-install))
- `3-install-nginx.sh`: Installs Nginx and auto-configures a front-end reverse proxy for Guacamole (optional)
- `4a-install-ssl-self-signed-nginx.sh`: Configures self-signed SSL certificates for Nginx proxy (optional)
- `4b-install-ssl-letsencrypt-nginx.sh`: Installs and configures Let's Encrypt with Guacamole and Nginx proxy (optional)
- `add-auth-duo.sh`: Adds the Duo MFA extensions if not selected during install (optional)
- `add-auth-ldap.sh`: Adds the Active Directory extension and setup template if not selected at install (optional)
- `add-auth-totp.sh`: Adds the TOTP MFA extension if not selected at install (optional)
- `add-ssl-guac-gaucd.sh`: A hardening script to wrap traffic between the guacd server and the Guacamole client (optional)
- `add-fail2ban.sh`: Adds and configures fail2ban to secure Guacamole against brute force attacks
- `add-smtp-relay-o365.sh`: Sets up a TLS/SMTP auth relay with O365 for monitoring and alerts (BYO app password)
- `backup-guacamole.sh`: A simple Guacamole backup script
- `branding.jar`: A customized Guacamole login screen with additional support for browser favicons (optional) (see: [Zer0CoolX/guacamole-customize-loginscreen-extension](https://github.com/Zer0CoolX/guacamole-customize-loginscreen-extension))

Special acknowledgments to [MysticRyuujin](https://github.com/MysticRyuujin/guac-install) and [Zer0CoolX](https://github.com/Zer0CoolX/guacamole-customize-loginscreen-extension) whose repositories provided helpful ideas for assembling this project.
