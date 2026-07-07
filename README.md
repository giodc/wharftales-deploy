# WharfTales Deploy

Easy deployment platform for web applications using Docker and Traefik.

# This was a proof of concept. it worked but still needs some refinement. other platform exist that do their job, anyway, it was fun. 

WharfTales is an open-source platform that makes deploying WordPress, PHP, and Laravel applications as simple as clicking a button. Built with Docker for security and portability, it provides a clean web interface for managing multiple applications on a single Ubuntu server.

## ✨ Features

- **🎯 One-Click Deployment**: Deploy WordPress, PHP, and Laravel apps instantly
- **🔒 Security First**: Docker containerization with SSL via Let's Encrypt
- **👥 User Management**: Role-based access control with admin and user roles
- **🔐 Two-Factor Authentication**: Optional TOTP-based 2FA for enhanced security
- **🔑 Site Permissions**: Granular access control - users can own or be granted access to specific sites
- **⚡ WordPress Optimized**: High-performance WordPress setup with Redis, OPcache, and CDN-ready configuration
- **🚀 Redis Support**: Built-in Redis caching for WordPress, PHP, and Laravel applications
- **🌐 Domain Management**: Support for test domains and custom domains
- **📱 Clean Web UI**: Modern, responsive interface for easy management
- **📊 Audit Logging**: Track all user actions and system changes
- **🔧 Zero Configuration**: Works out of the box on Ubuntu servers

## 🚀 Quick Start

### Installation

1. **Download and run the installer** (requires root privileges):
```bash
curl -fsSL https://raw.githubusercontent.com/giodc/wharftales/main/install.sh |  bash

```

2. **WharfTales starts automatically** after installation. To manage it manually:
```bash
cd /opt/wharftales
docker-compose up -d    # Start
docker-compose down     # Stop
docker-compose restart  # Restart
```

3. **Access the web interface**:
   - Open your browser and navigate to `http://your-server-ip:9000`
   - Complete the initial setup (create admin account)
   - Complete the setup wizard (configure domains, SSL, etc.)
   - Start deploying applications!

### First Application

1. Click "Deploy Your First App"
2. Choose application type (WordPress recommended for first time)
3. Enter a name and domain
4. Configure WordPress settings (if selected)
5. Enable SSL for custom domains
6. Click "Deploy Application"
7. Your app will be ready in under 60 seconds!

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Nginx Proxy   │────│  Web GUI (PHP)  │────│   MariaDB DB    │
│  (Port 80/443)  │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │              ┌────────▼────────┐             │
         │              │ Docker Socket   │             │
         │              │ (App Creation)  │             │
         │              └─────────────────┘             │
         │                                              │
         ▼                                              ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  WordPress App  │    │   PHP/Laravel   │    │   App Database  │
│  + Redis Cache  │    │      App        │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 📋 Supported Applications

### 🎯 WordPress (Highly Optimized)
- **Performance**: Redis object caching, OPcache, optimized PHP-FPM
- **Security**: Hardened configuration, security headers
- **Features**: Auto-install, plugin management, database optimization
- **CDN Ready**: Optimized for CDN integration

### 🐘 PHP Applications
- **Version**: PHP 8.2 with essential extensions
- **Features**: Apache web server, optimized configuration
- **Use Cases**: Custom PHP apps, frameworks, legacy applications

### 🔥 Laravel Applications
- **Performance**: Optimized PHP-FPM, OPcache, queue workers
- **Features**: Composer, Artisan commands, database migrations
- **Architecture**: Nginx + PHP-FPM with Supervisor

## 🔐 SSL Certificates

WharfTales automatically handles SSL certificates using Let's Encrypt:

- **Test Domains** (*.test.local): No SSL needed for local development
- **Custom Domains**: Automatic SSL certificate request and renewal
- **Requirements**: Domain must point to your server before SSL request

### SSL Certificate Management
- **Automatic Renewal**: Traefik automatically renews certificates 30 days before expiry
- **Certificate Duration**: 90 days (Let's Encrypt standard)
- **No Manual Intervention**: Renewal happens automatically in the background
- **Monitoring**: Check Traefik logs for renewal status: `docker logs wharftales_traefik`
- **Storage**: Certificates stored in `/letsencrypt/acme.json` within Traefik container

## 🎛️ Management

### Web Interface Features
- **Dashboard**: Overview of all deployed applications
- **Status Monitoring**: Real-time container status updates
- **Domain Management**: Easy domain and SSL configuration
- **One-Click Actions**: Start, stop, delete applications

### Command Line Management
```bash
# View running containers
docker ps

# Check logs
docker-compose logs -f [service]

# Restart services
docker-compose restart

# Update WharfTales
git pull && docker-compose build --no-cache
```

## 📁 Directory Structure

```
/opt/wharftales/
├── docker-compose.yml          # Main orchestration
├── install.sh                  # Installation script
├── gui/                        # Web interface
│   ├── index.php              # Main dashboard
│   ├── api.php                # REST API
│   └── includes/functions.php  # Core functions
├── nginx/                      # Nginx configuration
│   ├── nginx.conf             # Main config
│   └── sites/                 # Site-specific configs
├── apps/                       # Application templates
│   ├── wordpress/             # WordPress optimized setup
│   ├── php/                   # PHP application setup
│   └── laravel/               # Laravel application setup
├── data/                       # SQLite database
├── ssl/                        # SSL certificates
└── logs/                       # Application logs
```

## 🔧 Configuration

### Setup Wizard

After creating your admin account, you'll be guided through a setup wizard to configure:
- Custom wildcard domain for applications (e.g., `*.apps.example.com`)
- Custom domain for the dashboard (e.g., `deploy.example.com`)
- SSL certificate for the dashboard
- Let's Encrypt email for SSL notifications

**Re-running the Setup Wizard:**

If you need to access the setup wizard again after completing it:

1. **Force access via URL parameter:**
   ```
   http://your-server-ip:9000/setup-wizard.php?force=1
   ```

2. **Reset via database (requires admin access):**
   - Navigate to **Settings** in the web interface
   - Find the `setup_completed` setting
   - Change value from `1` to `0`
   - Refresh the dashboard to see the wizard

3. **Skip the wizard:**
   - Click "Skip Setup" during any step
   - Or access: `http://your-server-ip:9000/?skip_setup=1`

All settings configured in the wizard can be changed later from the **Settings** page.

### WordPress Performance Tweaks
- **OPcache**: Enabled with optimized settings
- **Redis**: Object caching for database query optimization
- **PHP-FPM**: Tuned for WordPress workloads
- **Nginx**: Gzip compression, browser caching, security headers
- **Database**: Optimized MySQL configuration

### Security Features
- **Container Isolation**: Each app runs in its own container
- **Security Headers**: XSS protection, CSRF protection
- **SSL/TLS**: Modern cipher suites, HSTS headers
- **Rate Limiting**: Built-in DDoS protection
- **File Permissions**: Proper ownership and permissions
- **User Authentication**: Secure session-based authentication with BCrypt password hashing
- **Two-Factor Authentication**: Optional TOTP-based 2FA with backup codes
- **Role-Based Access Control**: Admin and user roles with granular permissions
- **Audit Logging**: Complete audit trail of all user actions

## 👥 User Management & Permissions

### User Roles

**Admin**
- Full access to all features and sites
- Can create, edit, and delete users
- Can grant/revoke site permissions to other users
- Can view audit logs
- Can manage system settings

**User**
- Can view and manage assigned sites
- Can create new sites (if enabled by admin)
- Can access sites they own or have been granted permission to
- Limited to their own sites unless explicitly granted access

### Site Permissions

Sites have three permission levels:
- **View**: Read-only access to site information
- **Edit**: Can modify site settings and content
- **Manage**: Full control including deletion and advanced settings

### Managing Users (Admin Only)

1. Navigate to **Users** in the main menu
2. Click **Add User** to create new users
3. Set role (Admin/User) and permissions
4. Toggle "Can create sites" to control site creation ability
5. Click on **Manage Permissions** to grant site access to users

## 🔐 Two-Factor Authentication (2FA)

### Enabling 2FA

1. Click on your username in the top-right corner
2. Select **Two-Factor Auth**
3. Scan the QR code with your authenticator app (Google Authenticator, Authy, etc.)
4. Enter the 6-digit code to verify
5. Save your backup codes in a secure location

### Supported Authenticator Apps
- Google Authenticator
- Microsoft Authenticator
- Authy
- 1Password
- Any TOTP-compatible app

### Backup Codes
- 10 backup codes are generated when enabling 2FA
- Each code can be used once
- Store them securely - they're your backup if you lose access to your authenticator
- Backup codes are 8 characters long (e.g., `A1B2C3D4`)

### Disabling 2FA
1. Go to **Two-Factor Auth** settings
2. Enter your password to confirm
3. Click **Disable 2FA**

## 📊 System Requirements

### Minimum Requirements
- **OS**: Ubuntu 20.04 LTS or newer
- **RAM**: 2GB (4GB recommended for multiple WordPress sites)
- **Storage**: 20GB available space
- **CPU**: 1 core (2 cores recommended)

### Recommended for Production
- **RAM**: 8GB+ for multiple high-traffic WordPress sites
- **Storage**: 100GB+ SSD
- **CPU**: 4+ cores
- **Network**: Static IP address for SSL certificates

## 🐛 Troubleshooting

### Common Issues

**Port already in use (80/443)**
```bash
sudo netstat -tulpn | grep :80
sudo systemctl stop apache2  # If Apache is running
```

**Docker permission denied**
```bash
sudo usermod -aG docker $USER
# Logout and login again
```

**SSL certificate failed**
- Ensure domain points to server IP
- Check firewall allows ports 80/443
- Verify DNS propagation: `nslookup yourdomain.com`
- Check Traefik logs: `docker logs wharftales_traefik`

**SSL certificate renewal monitoring**
```bash
# Check Traefik logs for renewal activity
docker logs wharftales_traefik 2>&1 | grep -i "renew\|certificate"

# View current certificates
docker exec wharftales_traefik cat /letsencrypt/acme.json

# Check Traefik dashboard for certificate status
# Access: http://your-server:8080/dashboard/
```

### Getting Help
- **Logs**: Check `docker-compose logs` for detailed error information
- **Status**: Use the web interface to monitor application status
- **Issues**: Report bugs on GitHub Issues page

## 📈 Performance Optimization

### WordPress Optimization
- Enable Redis caching in the web interface
- Use a CDN for static assets
- Optimize images before upload
- Regular database cleanup

### Server Optimization
```bash
# Increase file limits
echo "fs.file-max = 65535" >> /etc/sysctl.conf

# Optimize Docker
echo '{"log-driver": "json-file", "log-opts": {"max-size": "10m", "max-file": "3"}}' > /etc/docker/daemon.json

# Restart services
systemctl reload sysctl
systemctl restart docker
```

## 🤝 Contributing

We welcome contributions! Please see our contributing guidelines and feel free to submit pull requests.

### Development Setup
```bash
git clone https://github.com/giodc/wharftales.git
cd wharftales
docker-compose up -d
```

## 📄 Traefik

http://localhost:8080/dashboard/#/http/routers
http://localhost:9090/metrics

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- Docker community for containerization best practices
- Let's Encrypt for free SSL certificates
- WordPress community for optimization techniques
- Bootstrap for the clean UI framework

---

**Made with ❤️ for developers who value simplicity and performance**

For support, questions, or feature requests, please open an issue on GitHub.
