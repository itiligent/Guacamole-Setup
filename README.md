# Guacamole 1.5.1 Virtual Desktop/Jump Server Appliance

A menu based build & install script for Guacamole 1.5.1 with support for SSL reverse proxy, AD integration, Multi-Factor Authentication and further security hardening.

## Automatic Build, Install & Config Script

To install Guacamole, copy and paste the following command into your terminal:

```
wget https://raw.githubusercontent.com/itiligent/Guacamole-Setup/main/1-setup.sh && chmod +x 1-setup.sh && ./1-setup.sh
```

## Prerequisites

Before running the setup script, make sure you have the following prerequisites:

- Recent flavors of Ubuntu / Debian / Raspbian
- Minimum 8GB RAM and 40GB HDD
- Public or private DNS entries that match the default physical interface IP address (required for self SSL)
- Incoming access on TCP ports 22, 80, and 443

## Setup Menu Flow

The setup menu offers the following options:

### 1. MySql & Authentication Setup

This option allows you to install Guacamole with either a local MySQL database or a remote MySQL instance. Additional sub-options include:

- Add MySQL `mysql_secure_installation` settings to a local or remote MySQL instance
- Add Guacamole MFA and Authentication extensions, such as DUO, TOTP, and LDAP. Note that simultaneous TOTP and DUO configurations are not possible.

### 2. Add Reverse Proxy Front End to Guacamole

This option enables you to add a reverse proxy front end to Guacamole. You can choose from the following sub-options:

- Install Nginx Reverse Proxy?: y/n (No = skip Nginx install and keep the default Guacamole front end e.g., `http://hostname:8080/guacamole`)
- Install Nginx with no SSL?: y/n (e.g., `http://hostname.local`)
- Install Nginx with self-signed SSL certificates?: y/n (e.g., `https://hostname.local`. This generates Windows and Linux browser certificates (`$site.crt`, `$site.key`, and `$site.pfx`) in the `$DOWNLOAD_DIR/guac-setup` directory and configures SSL with Nginx.)
- Install Nginx with Let's Encrypt certificates?: y/n (e.g., `https://public.site.com`. This configures Nginx with a newly created LetsEncrypt certificate and sets up auto renewals.)

### 3. Optional Hardening Scripts

After the installation, you can manually run optional hardening scripts for:

- Adding a fail2ban lockdown policy for Guacamole
- Encrypting internal traffic between the Guacamole client and Guacd daemon with SSL
- Integrating with Active Directory (See ACTIVE-DIRECTORY-HOW-TO.md) 
- Adding email alerts via Microsoft365 (SMTP auth)

To-Do List:

- Hardening scripts for Nginx
- MFA for shell access

## Installation Notes

Please note the following instructions for installation:

##### Note 1:

To create an unattended setup or further customise the setup script, follow these steps:
1. Run the wget link mentioned above.
2. When prompted during the `1-setup.sh` script, exit the script (at this point, only the scripts are downloaded).
3. Edit the "Silent setup options" section of `1-setup.sh`. Any variables with an actual setting (e.g., `VARIABLE="value"`) will not prompt during the interactive setup. With the right combination of variable inputs, it is possible to deploy Guacamole, Nginx, and SSL with zero touch.
3. Do not manually run `1-setup.sh` as sudo; it will prompt for sudo privileges as needed.


##### Note 2:

If you have edited `1-setup.sh`, ensure that you run the setup script saved locally with `./1-setup.sh` instead of using the web link. Running the online setup link again will re-download all scripts and overwrite any script changes you have made. To keep your script adaptations and avoid overwriting, comment out the relevant wget lines in the "Download GitHub Setup" section at the top of `1-setup.sh` before running setup locally. Commented out scripts will not be re-downloaded, and the installer will work from the local customised copy.


There should be no need to customise any scripts other than `1-setup.sh`. Be aware that all optional (manually run) "add-x.sh" scripts are dynamically updated during the installation with the relevant variables you may have selected. Editing anything other than `1-setup.sh` may break this functionality, so make changes only if you understand the impacts.

### Items Downloaded with the Setup Command

The setup command mentioned above downloads the following items into the `$DOWNLOAD_DIR/guac-setup` directory:

- `1-setup.sh`: The parent install script itself
- `2-install-guacamole.sh`: Guacamole installation script (inspired by [MysticRyuujin/guac-install](https://github.com/MysticRyuujin/guac-install))
- `3-install-nginx.sh`: Installs Nginx and auto-configures a front-end reverse proxy for Guacamole (optional)
- `4a-install-ssl-self-signed-nginx.sh`: Configures self-signed SSL certificates for Nginx proxy (optional)
- `4b-install-ssl-letsencrypt-nginx.sh`: Installs and configures Let's Encrypt with Guacamole and Nginx proxy (optional)
- `add-auth-duo.sh`: Adds the Duo MFA extensions if not selected during install (optional)
- `add-auth-ldap.sh`: Adds the LDAP Active Directory extension and provides LDAP setup requirements (optional)
- `add-auth-totp.sh`: Adds the TOTP MFA extension if not selected during install (optional)
- `add-ssl-guac-gaucd.sh`: A hardening script to wrap traffic between the guacd server and the Guacamole client (optional)
- `add-fail2ban.sh`: Adds and configures fail2ban to secure Guacamole against brute force attacks
- `add-smtp-relay-o365.sh`: Sets up a TLS/SMTP auth relay with O365 for monitoring and alerts (BYO app password)
- `backup-guacamole.sh`: A simple Guacamole backup script
- `branding.jar`: A customized Guacamole login screen with additional support for browser favicons (optional) (see: [Zer0CoolX/guacamole-customize-loginscreen-extension](https://github.com/Zer0CoolX/guacamole-customize-loginscreen-extension))

Special acknowledgments to [MysticRyuujin](https://github.com/MysticRyuujin/guac-install) and [Zer0CoolX](https://github.com/Zer0CoolX/guacamole-customize-loginscreen-extension) whose repositories provided helpful ideas for assembling this project.