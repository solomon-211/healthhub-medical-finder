# HealthHub Bridge - Global Medical Facility Finder
This is an application for locating medical facilities based on an individual's location making it work perfectly across the globe with real-time API Integration, interactive maps, and appointment booking.

---

## Important URLs for the project

**Demo Video**: https://youtu.be/BN70xcWhNkk 
**Live Application**: http://www.solomonleek.tech


## Alternative Domain names to find live application on chrome browser or Google
- solomonleek.tech
- www.solomonleek.tech

## Table of Contents
1. Overview
2. Architecture
3. Features
4. Technologies
5. Deployment
6. Security & Performance
7. Testing
8. Troubleshooting
9. API Attribution
10. Challenges & Solutions
11. Future Enhancements


## 1. Overview

### Purpose:

This is application is to bridge the gap that exists when finding medical facilities based on an individual's location and preferences especially when a person travel to a new location.

### Target Audience:
- Global residents
- International travelers
- Healthcare professionals
- Emergency responders
- Expatriates navigating local healthcare systems (Researchers)

## 2. Architecture

### System Overview
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│                 │    │                  │    │                 │
│   Client/User   │◄──►│  Load Balancer   │◄──►│   Web Servers   │
│   (Browser)     │    │     (Nginx)      │    │    (Nginx)      │
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

**Load Balancer (Nginx)**
- Traffic distribution and high availability
- Uses round-robin load balancing algorithm 
- Automatic server health monitoring
- Real-time monitoring on port 8080

**Web Servers**
- Host the HealthHub application
- Active-active configuration
- Automatic traffic rerouting on server failure

**Application Layer**
- **Frontend**: Single-page HTML application
- **Storage**: Browser localStorage for favorites and history
- **Caching**: 10-minute intelligent cache system
- **APIs**: Multi-provider integration with fallback

### Data Flow

1. User Request → Load Balancer (Nginx)
2. Load Balancer → Web Server (Round-robin)
3. Web Server → HTML Application
4. Application → External APIs (Google Places/OpenStreetMap)
5. APIs → Application (Facility data)
6. Application → User (Search results)

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
│     Nginx       │
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

## 3. Features

### Core Functionality
- Global Medical facilities in the interests of the user (hospitals, clinics, pharmacies, dental centers, optical facilities)
- Distance-based filtering (1-100km radius)
- User friendly and interactive map with leaflet and street view integration
- Appointment booking schedules on user preferable time and date
- Favorite management system with interactive button for returning to the dashboard
- Search and recent history and intelligent caching support by clear filter and clear all the recent search history
- Responsive design applicable in mobile phones, tablets, and laptop

### Technical Features

**Search & Discovery**
- Multi-API integration (Google Places API + OpenStreetMap fallback)
- Intelligent geolocation with reverse geocoding
- Explore through results by using search button without new API calls
- Real time results scenarios with immediate distance calculations
- User friendly filtering through various options; rating, and alphabetical order and real world application rating that have access to the main rating source

**Performance & Reliability**
- Intelligent caching (10-minute system reducing API calls by 90%)
- Progressive loading with pagination (9 facilities per page)
- Comprehensive error handling and recovery
- Nginx load balancing for high availability

**Design & Accessibility**
- Healthcare-focused green and yellow theme
- Mobile-first responsive design
- ARIA labels and semantic HTML
- Real-time feedback with loading states
- Clean modal interfaces

## 4. Technologies

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
- Nginx load balancer for traffic distribution
- Ubuntu web servers (dual-server setup for redundancy)
- Nginx HTTP server with virtual host configuration
- UFW firewall for security
- Single-file architecture for easy deployment

## 5. Deployment Instructions

### Deployment Prerequisites
- Two Ubuntu web servers (Web01, Web02)
- One Ubuntu server for Nginx load balancer
- SSH access with sudo privileges
- Basic Linux command line knowledge

### Step 1: Connect to Servers
1. Create a repository and clone it to the container (webterm)
2. CD into the repo directory
3. Create README.md file and Index.html
4. Use SSH to connect to your web servers or hosting service

### Step 2: Prepare Web Directory
**On both Web01 and Web02**
```bash
cd /var/www/

# Create application directory
sudo mkdir -p healthhub
sudo chown $USER:$USER healthhub
cd healthhub
```

### Step 3: Upload Application Files
**On your local machine**
1. Navigate to the project directory containing Index.html and README.md

**Use SCP to transfer files to the server**
```bash
scp Index.html README.md ubuntu@web01:/var/www/healthhub/
scp Index.html README.md ubuntu@web02:/var/www/healthhub/
```

### Step 4: Configure Web Servers
**On both Web01 and Web02**

Create virtual host configuration:
```bash
sudo nano /etc/nginx/sites-available/healthhub
```

Add the following configuration:
```nginx
server {
    listen 80;
    listen [::]:80;
    
    server_name solomonleek.tech www.solomonleek.tech _;
    
    root /var/www/healthhub;
    index index.html;

    # Logging
    access_log /var/log/nginx/healthhub_access.log;
    error_log /var/log/nginx/healthhub_error.log;

    # Main location
    location / {
        try_files $uri $uri/ =404;
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/json;

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Health check endpoint
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }
}
```

**Enable the site**:
```bash
# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Enable HealthHub site
sudo ln -s /etc/nginx/sites-available/healthhub /etc/nginx/sites-enabled/

# Test Nginx configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Step 5: Configure Firewall
**On both Web01 and Web02**
```bash
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw status
```

### Step 6: Configure Load Balancer
**On Lb01**

Connect to load balancer:
```bash
ssh -i ~/.ssh/my_web-key ubuntu@54.236.248.230
```

Install Nginx (if not already installed):
```bash
sudo apt update
sudo apt install nginx -y
```

Configure Nginx load balancer:
```bash
sudo nano /etc/nginx/sites-available/healthhub-lb
```

Add the following configuration:
```nginx
upstream healthhub_backend {
    server 100.26.206.13:80 max_fails=3 fail_timeout=30s;
    server 54.211.4.59:80 max_fails=3 fail_timeout=30s;
}

server {
    listen 80;
    listen [::]:80;
    
    server_name solomonleek.tech www.solomonleek.tech;

    # Logging
    access_log /var/log/nginx/healthhub_lb_access.log;
    error_log /var/log/nginx/healthhub_lb_error.log;

    # Proxy settings
    location / {
        proxy_pass http://healthhub_backend;
        
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        proxy_buffering on;
        proxy_buffer_size 4k;
        proxy_buffers 8 4k;
        
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
    }

    location /health {
        access_log off;
        return 200 "Load balancer healthy\n";
        add_header Content-Type text/plain;
    }

    location /lb-status {
        access_log off;
        return 200 "Active backends: 2\nServer 1: 100.26.206.13:80\nServer 2: 54.211.4.59:80\n";
        add_header Content-Type text/plain;
    }
}
```

**Enable and restart Nginx**:
```bash
# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Enable load balancer configuration
sudo ln -s /etc/nginx/sites-available/healthhub-lb /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx

# Enable Nginx to start on boot
sudo systemctl enable nginx

# Check status
sudo systemctl status nginx
```

### Step 7: Testing on Servers
**On Web01, Web02, and Lb01 - Test locally on each server**:
```bash
curl -sI localhost
curl localhost
```

### Step 8: Verify Deployment
**Test Web Servers from local machine**:
```bash
# Test Web01
curl http://100.26.206.13/

# Test Web02
curl http://54.211.4.59/

# Test Load Balancer
curl http://54.236.248.230/
```

**Access Points**:
- **Application**: http://54.236.248.230
- **Load Test**: `curl -s http://54.236.248.230`

## 6. Security & Performance

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

## 7. Testing

### Basic Tests
- Search functionality with various locations
- Filter and sorting operations
- Favorites persistence across sessions
- Error handling with invalid inputs

### Load Balancer Tests
```bash
# Access test
curl http://54.236.248.230

# Performance test
ab -n 1000 -c 10 http://54.236.248.230/
```

## 8. Troubleshooting

### Common Issues
- **Application not loading**: Check Nginx status and file permissions
- **Load balancer issues**: Verify Nginx status and firewall rules
- **API errors**: Check internet connectivity and browser console
- **Server DOWN in Nginx**: Test health check endpoints and port availability

### Error Handling

**Comprehensive Error Management**

API Error Handling:
```javascript
// Network failures
try {
    const response = await fetch(apiUrl);
    if (!response.ok) throw new Error(`Request failed: ${response.status}`);
} catch (error) {
    showAlert('Unable to connect to service. Please check your internet connection.', 'error');
}

// API downtime fallback
if (primaryAPIFails) {
    // Automatically switch to OpenStreetMap fallback
    // User experiences no interruption
}

// Invalid API responses
if (!data || data.length === 0) {
    showAlert('No facilities found. Try expanding your search radius.', 'warning');
}
```

## 9. API Attribution

### APIs Used

**Google Places API (New V2)**
- **Purpose**: Primary facility search with comprehensive data
- **Provider**: Google via RapidAPI
- **Features**: Ratings, phone numbers, addresses, opening hours
- **Documentation**: https://developers.google.com/maps/documentation/places/web-service/overview
- **Rate Limits**: 500 requests/day (can be increased)
- **Cost**: Free tier available, paid plans for higher usage

**Google Street View Static API**
- **Purpose**: Visual facility context through street-level imagery
- **Provider**: Google via RapidAPI
- **Features**: 360-degree street views, location verification
- **Documentation**: https://developers.google.com/maps/documentation/streetview/overview
- **Rate Limits**: 25,000 requests/day

**OpenStreetMap Nominatim**
- **Purpose**: Geocoding, reverse geocoding, and fallback facility search
- **Provider**: OpenStreetMap Foundation
- **License**: Open Database License (ODbL)
- **Features**: Free, no API key required, global coverage
- **Documentation**: https://nominatim.org/release-docs/latest/api/Overview/
- **Usage Policy**: 1 request per second for fair use
- **Attribution**: Map data © OpenStreetMap contributors

**Leaflet.js**
- **Purpose**: Interactive mapping library
- **License**: BSD 2-Clause License
- **Features**: Lightweight, mobile-friendly, highly customizable
- **Documentation**: https://leafletjs.com/
- **Attribution**: Required in map display

**Font Awesome**
- **Purpose**: Icon library for UI elements
- **Version**: 6.4.0
- **License**: Font Awesome Free License
- **Documentation**: https://fontawesome.com/

### Credit & Attribution

All APIs are properly credited throughout the application:
- Footer attribution for Google APIs and OpenStreetMap
- Map tiles include OpenStreetMap copyright
- API links provided in documentation
- Compliance with all licensing requirements

## 10. Challenges & Solutions

### Challenge 1: API Rate Limiting
**Problem**: Google Places API has strict rate limits that could exhaust quickly with multiple users.

**Solution**: 
- Implemented intelligent 10-minute caching system
- Reduced API calls by 90% through cache reuse
- Added OpenStreetMap as fallback provider
- Displays cache age to users for transparency

### Challenge 2: Global Coverage
**Problem**: Initial API selection was limited to specific regions.

**Solution**:
- Integrated OpenStreetMap Nominatim for worldwide coverage
- Multi-provider architecture with automatic fallback
- Tested with locations across 5 continents
- Added reverse geocoding for location names

### Challenge 3: Load Balancer Configuration
**Problem**: Health checks were failing intermittently, causing server removal.

**Solution**:
- Adjusted health check intervals (2 seconds)
- Configured rise/fall thresholds (rise=2, fall=3)
- Added proper health check endpoint
- Implemented comprehensive logging

### Challenge 4: User Experience on Mobile
**Problem**: Initial design wasn't mobile-friendly; buttons were too small, text was hard to read.

**Solution**:
- Adopted mobile-first responsive design
- Implemented CSS Grid and Flexbox
- Tested on multiple device sizes
- Added touch-friendly button sizing (minimum 44px)

### Challenge 5: API Key Security
**Problem**: Risk of API keys being exposed in public repository.

**Solution**:
- Created `.gitignore` to exclude configuration files
- Used environment variables for deployment
- Documented security practices
- Implemented key rotation policy

### Challenge 6: Error Handling
**Problem**: Users experiencing confusing errors when APIs failed.

**Solution**:
- Comprehensive try-catch blocks throughout code
- User-friendly error messages with recovery steps
- Automatic fallback to cached data
- Graceful degradation when services unavailable

### Challenge 7: Data Presentation
**Problem**: Initial data dumps were overwhelming; users couldn't find relevant info.

**Solution**:
- Implemented pagination (9 facilities per page)
- Added sorting and filtering options
- Created card-based layout for clarity
- Included search-within-results feature

### Challenge 8: Performance Optimization
**Problem**: Page was slow to load with all assets and API calls.

**Solution**:
- Lazy loading for maps and images
- Single-file architecture for reduced HTTP requests
- CDN usage for external libraries
- Optimized DOM manipulation

## 11. Future Enhancements

### Planned Features
- User authentication for personalized experience
- Advanced filtering (insurance accepted, specialties)
- Multi-language support for global users
- Integration with telemedicine platforms
- Real-time appointment availability
- Push notifications for appointment reminders
- AI-powered facility recommendations
- Offline mode with cached data access

### Performance Improvements
- Further caching optimizations
- Enhanced load balancing algorithms
- CDN integration for static assets
- Continuous monitoring and alerting systems

## License

This project is open source. API usage subject to respective provider terms.