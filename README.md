# HealthHub Bridge - Global Medical Facility Finder
This is an application for locating medical facilities based on an individual's location making it work perfectly across the globe with real-time API Integration, interactive maps, and appointment booking.

## DEMO VIDEO
https://youtu.be/mNGpVPCMgJk

## Table of Contents
- Overview
- architecture
- Features
- Technologies
- Quick Start
- Deployment
- Security & Performance
- Testing
- Troubleshooting
- API Attribution
- License


## Overview

### Purpose:

This is application is to bridge the gap that exists when finding medical facilities based on an individual's location and preferences especially when a person travel to a new location.

### Target Audience:
- Global residents
- international travelers
- healthcare professionals
- emergency responders
- expatriates navigating local healthcare systems (Researchers)
## Architecture

### System Overview
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│                 │    │                  │    │                 │
│   Client/User   │◄──►│  Load Balancer   │◄──►│   Web Servers   │
│   (Browser)     │    │   (HAProxy)      │    │  (Apache/HTML)  │
│                 │    │                  │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │                         │
                              │                         │
                              ▼                         ▼
                    ┌──────────────────┐    ┌─────────────────┐
                    │                  │    │                 │
                    │  Stats Dashboard │    │  External APIs  │
                    │   (Port 8080)    │    │ Google Places   │
                    │                  │    │ Street View     │
                    └──────────────────┘    │ OpenStreetMap   │
                                            └─────────────────┘
```

### Infrastructure Components

#### Load Balancer (HAProxy)
- Traffic distribution and high availability
- Uses round-robin load balancing algorthim 
- Automatic server health monitoring
- Real-time monitoring on port 8080

#### Web Servers (Apache)
- Host the HealthHub application
- Active-active configuration
- Automatic traffic rerouting on server failure

#### Application Layer
- **Frontend**: Single-page HTML application
- **Storage**: Browser localStorage for favorites and history
- **Caching**: 10-minute intelligent cache system
- **APIs**: Multi-provider integration with fallback

### Data Flow

1. User Request: Load Balancer (HAProxy)
2. Load Balancer: Web Server (Round-robin)
3. Web Server: HTML Application
4. Application: External APIs (Google Places/OpenStreetMap)
5. APIs: Application (Facility data)
6. Application: User (Search results)

### Security Architecture

```
┌─────────────────┐
│   UFW Firewall  │
│   Port 22: SSH  │
│   Port 80: HTTP │
│   Port 8080: Stats │
└─────────────────┘
         │
         ▼
┌─────────────────┐
│   HAProxy       │
│   - Rate Limiting│
│   - Health Checks│
│   - Load Balance │
└─────────────────┘
         │
         ▼
┌─────────────────┐
│   Web Servers   │
│   - Input Valid │
│   - XSS Protect │
│   - No Secrets  │
└─────────────────┘
```

## Features

### Core Functionality
- Global Medical facilities in the interests of the user; (hospitals, clinics, pharmacies, dental centers, optical facilities)
- Distance-based filtering (1-100km radius)
- User friendly and interactive map with leaflet and street view integration
- Appointment booking schedules on user preferable time and date.
- Favorite management system with interactive button for returning to the dashboard
- Search and recent history and intelligent caching support by clear filter and clear all the recent search history
- Responsive design applicable in mobile phones, tablets, and laptop

## Technical Features

### Search & Discovery
- Multi-API integration (Google Places API + OpenStreetMap fallback)
- Intelligent geolocation with reverse geocoding
- Explore through results by using search button without new API calls
- Real time results scenarios with imediate distance calculations.
- User friendly filtering through vaious options; rating, and alphabetical order and real world application rating that have access to the main rating source.

### Performance & Reliability
- Intelligent caching (10-minute system reducing API calls by 90%)
- Progressive loading with pagination (9 facilities per page)
- Comprehensive error handling and recovery
- Offline resilience with cached data
- HAProxy load balancing for high availability

### Design & Accessibility
- Healthcare-focused green and yellow theme
- Mobile-first responsive design
- ARIA labels and semantic HTML
- Real-time feedback with loading states
- Clean modal interfaces

## Technologies

### Frontend
- HTML5 with semantic markup and accessibility features
- CSS3 with flexbox, grid, animations, and responsive design
- Vanilla JavaScript (ES6+) for optimal performance
- Font Awesome 6.4.0 for icons
- Leaflet 1.9.4 for interactive maps

### APIs
- **Google Places API (V2)**: Primary facility search with ratings and contact details
- **Google Street View API**: High-quality street-level imagery via RapidAPI
- **OpenStreetMap Nominatim**: Free geocoding and fallback facility search

### Infrastructure
- HAProxy load balancer for traffic distribution
- Ubuntu web servers (dual-server setup for redundancy)
- Apache HTTP server with virtual host configuration
- UFW firewall for security
- Single-file architecture for easy deployment

## Quick Start

### Local Development

1. Clone the repository:
```bash
git clone https://github.com/solomon-211/healthhub-medical-finder.git
cd healthhub-medical-finder
```

## Deployment instruction
To deploy the HealthHub Medical Finder application, follow these steps:

Step 1: Connect to Servers
- Use SSH to connect to your web server or hosting service.
Step 2: Prepare Web Directory
### On both Web01 and Web02
- cd /var/www/html

### Create application directory
- sudo mkdir -p healthhub
- sudo chown $USER:$USER healthhub
- cd healthhub

Step 3: Upload Application Files
## First option
### On your local machine

- Navigate to the project directory containing Index.html and README.md.

### Use SCP to transfer files to the server

- scp Index.html README.md ubuntu@web01:/var/www/html/healthhub/
- scp Index.html README.md ubuntu@web02:/var/www/html/healthhub/


Step 5: Configure Web Server
For Apache:
bash# Create virtual host configuration
sudo nano /etc/apache2/sites-available/healthhub.conf
### Add the following configuration:
apache<VirtualHost *:80>
    ServerName healthhub.solomon-leek.tech
    DocumentRoot /var/www/html/healthhub
    
    <Directory /var/www/html/healthhub>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/healthhub-error.log
    CustomLog ${APACHE_LOG_DIR}/healthhub-access.log combined
</VirtualHost>

### Enable the site:
- bashsudo a2ensite healthhub.conf
- sudo systemctl reload apache2

## For Nginx:
- bash# Create server block

sudo nano /etc/nginx/sites-available/healthhub
### Add the following configuration:
nginxserver {
    listen 80;
    server_name healthhub.solomon-leek.tech;
    root /var/www/html/healthhub;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    access_log /var/log/nginx/healthhub-access.log;
    error_log /var/log/nginx/healthhub-error.log;
}
### Enable the site:
1. sudo ln -s /etc/nginx/sites-available/healthhub /etc/nginx/sites-enabled/
2. sudo nginx -t
3. sudo systemctl reload nginx

### Step 6: Verify Deployment
bash# Test Web01
curl http://100.26.206.13/

## Test Web02
curl http://54.211.4.59/

## Load Balancer Configuration
- The load balancer (Lb01) distributes incoming traffic between Web01 and Web02, ensuring high availability and optimal performance.

## Configuration Steps
- Step 1: Connect to Load Balancer

ssh -i ~/.ssh/my_web-key ubuntu@54.236.248.230

### Step 2: Install HAProxy (if not already installed)
1. sudo apt update
2. sudo apt install haproxy -y

### Step 3: Configure HAProxy
- sudo nano /etc/haproxy/haproxy.cfg

### Add the following configuration:
haproxyglobal
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000
    errorfile 400 /etc/haproxy/errors/400.http
    errorfile 403 /etc/haproxy/errors/403.http
    errorfile 408 /etc/haproxy/errors/408.http
    errorfile 500 /etc/haproxy/errors/500.http
    errorfile 502 /etc/haproxy/errors/502.http
    errorfile 503 /etc/haproxy/errors/503.http
    errorfile 504 /etc/haproxy/errors/504.http

## Frontend configuration
frontend healthhub_frontend
    bind *:80
    default_backend healthhub_backend
    
    # Optional: Add custom headers
    http-request set-header X-Forwarded-For %[src]
    http-request set-header X-Forwarded-Proto http

## Backend configuration with health checks
backend healthhub_backend
    balance round robin
    option httpchk GET /healthhub/
    http-check expect status 200
    
    # Server definitions
    server web01 web01.solomon-leek.tech:80 check inter 2000 rise 2 fall 3
    server web02 web02.solomon-leek.tech:80 check inter 2000 rise 2 fall 3

## Statistics page (optional but recommended)
listen stats
    bind *:8080
    stats enable
    stats uri /stats
    stats refresh 30s
    stats auth admin:your_secure_password
Step 4: Validate and Restart HAProxy
bash# Validate configuration
sudo haproxy -c -f /etc/haproxy/haproxy.cfg

## Restart HAProxy
sudo systemctl restart haproxy

## Enable HAProxy to start on boot
sudo systemctl enable haproxy

## Check status
sudo systemctl status haproxy


2. Run locally:
```bash
# Using Python
python3 -m http.server 8000

# OR open directly
open index.html
```

### Testing on browser
- **Load Balancer**: http://54.236.248.230
- **Domain**: http://www.solomonleek.tech
- **Web Servers**: http://100.26.206.13, http://54.211.4.59

### Deployment Prerequisites
- Two Ubuntu web servers (Web01, Web02)
- One Ubuntu server for HAProxy load balancer
- SSH access with sudo privileges
- Basic Linux command line knowledge

### Web Servers Setup (Web01 & Web02)

1. **Install Apache**:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

2. **Configure Virtual Host**:
```bash
sudo mkdir -p /var/www/healthhub
sudo nano /etc/apache2/sites-available/healthhub.conf
```

Add configuration:
```apache
<VirtualHost *:80>
    ServerAdmin admin@healthhub.local
    DocumentRoot /var/www/healthhub
    <Directory /var/www/healthhub>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

3. **Enable Site**:
```bash
sudo a2dissite 000-default.conf
sudo a2ensite healthhub.conf
sudo systemctl reload apache2
```

4. **Deploy Files**:
```bash
scp index.html user@server_ip:/var/www/healthhub/
```

5. **Configure Firewall**:
```bash
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
```

### HAProxy Load Balancer Setup

1. **Install HAProxy**:
```bash
sudo apt install haproxy -y
```

2. **Configure HAProxy** (`/etc/haproxy/haproxy.cfg`):
```haproxy
frontend healthhub_frontend
    bind *:80
    default_backend healthhub_backend

backend healthhub_backend
    balance roundrobin
    server web01 100.26.206.13:80 check
    server web02 54.211.4.59:80 check

listen stats
    bind *:8080
    stats enable
    stats uri /haproxy?stats
```

3. **Start Services**:
```bash
sudo systemctl restart haproxy
sudo systemctl enable haproxy
```

### Verification
- **Application**: http://54.236.248.230
- **Stats Dashboard**: http://54.236.248.230:8080/haproxy?stats
- **Load Test**: `curl -s http://54.236.248.230`

## Security & Performance

### Security Features
- UFW firewall with restricted port access (22, 80, 443)
- Input validation and XSS protection
- No sensitive data exposure
- HTTPS-ready configuration

### Performance Optimizations
- Intelligent caching (10-minute system, 90% API call reduction)
- Progressive pagination (9 facilities per page)
- Lazy map loading and event delegation
- Dual API strategy with graceful fallbacks
- Single-file architecture with CDN resources

## Testing

### Basic Tests
- Search functionality with various locations
- Filter and sorting operations
- Favorites persistence across sessions
- Error handling with invalid inputs

### Load Balancer Tests
``
# Access test
curl http://54.236.248.230

# Performance test
ab -n 1000 -c 10 http://54.236.248.230/
``

## Troubleshooting

### Common Issues
- **Application not loading**: Check Apache status and file permissions
- **Load balancer issues**: Verify HAProxy status and firewall rules
- **API errors**: Check internet connectivity and browser console
- **Server DOWN in HAProxy**: Test health check endpoints and port availability

### Quick Fixes
```bash
# Check services
sudo systemctl status apache2
sudo systemctl status haproxy

# Check logs
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/haproxy.log

# Test connectivity
curl http://100.26.206.13
```

## API Attribution

- **Google Places API**: Primary facility search via RapidAPI
- **Google Street View API**: Visual facility context via RapidAPI  
- **OpenStreetMap Nominatim**: Free geocoding and fallback search (ODbL License)
- **Leaflet**: Interactive mapping library (BSD 2-Clause License)
- **Font Awesome**: Icon library

Map data © OpenStreetMap contributors

## License

This project is open source. API usage subject to respective provider terms.

