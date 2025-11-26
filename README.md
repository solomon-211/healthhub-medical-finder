# HealthHub Bridge - Global Medical Facility Finder
This is an application for locating medical facilities based on an individual's location, making it work perfectly across the globe with real-time API Integration, interactive maps, and appointment booking.
---
## Important URLs for the project

**Demo Video**: https://youtu.be/BN70xcWhNkk
**Live Application**: http://www.solomonleek.tech

## Alternative Domain names to find live application on Chrome browser or Google
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

## 1. Overview of the Application
HealthHub Bridge is a web-based application dedicated to helping users find nearby medical facilities based on the user's location and preferences, with comprehensive information and real-time data about the medical facilities fetched from multiple APIs to provide accurate results and data.

### Target Audiences
- Travellers are desperate for medical facilities based on the new location
- Researchers

## 2. Architecture

### System Overview
```
__________________     ____________________    ___________________
│                 │    │                  │    │                 │
│   Client/User   │◄──►│  Load Balancer   │◄──►│   Web Servers   │
│     (Browser)   │    │     (Nginx)      │    │    (Nginx)      │
│_________________│    │__________________│    │_________________│


                              │                         │
                              │                         │
                              ▼                         ▼
                    ___________________     __________________    
                    │                  │    │                 │
                    │  Stats Dashboard │    │  External APIs  │
                    │   (Port 8080)    │    │ Google Places   │
                    │                  │    │ Street View     │
                    |__________________|    │ OpenStreetMap   |
                                            │_________________|
                                       
```

### Infrastructure Components

**Load Balancer (Nginx)**
- Traffic distribution and high availability
- Uses the round-robin load balancing algorithm
- Automatic server health monitoring
- Real-time monitoring on port 8080

**Web Servers**
- Host the HealthHub application
- Active-active configuration
- Automatic traffic rerouting on server failure
- UFW firewall for enhanced security

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
|__________________|
         │
         ▼
__________________
│     Nginx       │
│   - Rate Limiting│
│   - Health Checks│
│   - Load Balance │
|__________________|
         │
         ▼
___________________
│   Web Servers   │
│   - Input Valid │
│   - XSS Protect │
│   - No Secrets  │
|_________________|

```
## 3. Features

### Core Functionality
- Global Medical facilities in the interests of the user (hospitals, clinics, pharmacies, dental centers, optical facilities)
- Max distance filtering manipulation (1-100km radius)
- User-friendly and interactive map with leaflet and street view integration
- Appointment booking schedules on the user's preferred time and date
- Favorite management system with an interactive button for returning to the dashboard and pressing the heart icon to remove the favorite medical facility from the favorites
- Search and recent history, and intelligent caching support by clear filter and clear all the recent search history
- Responsive design applicable to mobile phones, tablets, and laptops

### Technical Features

**Search & Discovery**
- Multi-APIs Integration (Google Places API (V2), Google Street View API, and OpenStreetMap fallback)
- Navigate the search results by using the search button to find a medical facility without API calls
- User-friendly filtering through various options; rating, alphabetical order, and real real-world application rating that has access to the main rating source
- Real-time results scenarios with immediate distance calculations

**Performance & Reliability**
- Caching intelligence that reduced system calling APIs by 90%
- Exploring medical facilities by using pagination (9 medical facilities per page)
- User-friendly error handling and recovery
- Nginx load balancing for high availability to reduce traffic distribution between the two web servers.

**Design & Accessibility**
- HealthHub Bridge primary colors used green and yellow theme
- Mobile web-based application responsive design
- ARIA labels and semantic HTML
- User-friendly feedback with loading states
- Visually appealing modal interfaces

## 4. Technologies

### Frontend
- HTML5 with semantic markup and accessibility features
- CSS3 with flexbox, grid, animations, and responsive design
- Vanilla JavaScript (ES6+) for optimal performance
- Font Awesome 6.4.0 for icons
- Leaflet 1.9.4 for interactive maps

### APIs
- **Google Places API (V2)**: Primary medical facility search rendering ratings, address, and contact details
- **Google Street View API**: High quality street-view level map via RapidAPI
- **OpenStreetMap Nominatim**: Free geocoding and fallback facility search

### Infrastructure
- Nginx load balancer focuses on traffic distribution between the two servers
- Web servers for setting up the web-based application
- UFW firewall for security purposes

## 5. Deployment Instructions

### Deployment Prerequisites
- Two Ubuntu web servers (Web01, Web02)
- One Ubuntu server for Nginx load balancer
- SSH access with sudo privileges
- Basic Linux command line knowledge
- Nginx is installed on all servers

### Step 1: Connect to Servers
1. Create a repository and clone it to the container (webterm)
2. CD into the repo directory
3. Create the scripts file:
    - README.md
    - Index.html
4. Use SSH to connect to your web servers or hosting service

### Step 2: Directory Creation
**On both Web01 and Web02**
```bash
cd /var/www/

# Create application directory
sudo mkdir -p healthhub
sudo chown $USER:$USER healthhub
cd healthhub
```
### Step 3: Upload Application files to the servers
**On your local machine**
1. Navigate to the project directory containing Index.html and README.md

**Use SCP to transfer files to the server**
```bash
scp Index.html README.md ubuntu@web01:/var/www/healthhub/
scp Index.html README.md ubuntu@web02:/var/www/healthhub/
```
### Step 4: Configuration of web servers
**On both Web01 and Web02**

Create virtual host configuration:
```bash
sudo nano /etc/nginx/sites-available/healthhub
```
### Then add the configuration scripts into
- /etc/nginx/sites-available/healthhub
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
Connect to the load balancer:
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
Add the configuration scripts for configuring the load balancer into
- healthhub-lb

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

### Load Balancer Tests
```bash
# Access test
curl http://54.236.248.230

# Performance test
ab -n 1000 -c 10 http://54.236.248.230/
```

## 6. Security & Performance

### Security Features
- UFW firewall exclusively for port access (22, 80, 443)
- Input validation and XSS protection
- No sensitive data exposure

## 7. Testing
### Basic Tests
- Search functionality for various locations
- Filter medical facilities based on hospitals, clinics, and optical centers
- Sort medical facilities based on their distance (Nearest medical facility), A-Z, Z-A, and rating
- Favorites persistence across sessions
- Appointment booking with medical facility functionality
- Error handling with invalid inputs

## 8. Troubleshooting

### Common Issues
- **Application not loading**: Check Nginx status and file permissions
- **Load balancer issues**: Verify Nginx status and firewall rules
- **API errors**: Check internet connectivity and browser console
- **Server DOWN in Nginx**: Test health check endpoints and port availability

### Error Handling
**Comprehensive Error Management**
API Error Handling:
```JavaScript
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
- Google places API (New v2) from RapidAPI(Rating, Phone numbers, address, and opening hours)
- Google street view static API from RapidAPI(visual facility context through street-level imagery)
- OpenStreetMap Nominatim(geocoding, reverse geocoding, and fallback facility search)
- leaflet.js for an interactive mapping library for map display

**Font Awesome**
-  Icon library for UI elements
-  Font Awesome Free License
-  https://fontawesome.com/

### Credit & Attribution
All APIs used in the web-based application are both credited in the application and the documentation file
- Footer attribution for APIs recognition
- OpenStreetMap copyright
- API links are documented in the README.md file
- Compliance with all licensing requirements.

## 10. Encounter challenges and solutions

### 1: Rate Limiting from API
**Problem**: Google Places API has strict rate limits that could be exhausted quickly with multiple users.

**Solution**:
- Implemented an intelligent 10-minute caching system
- Reduced API calls by about 90%
- Used OpenStreetMap as a fallback if Google doesn't work
- Displays cache age to users for transparency

### 2: Global Coverage
**Problem**: Initial API selection was limited to specific countries.

**Solution**:
- Used integrated OpenStreetMap Nominatim so that the application is global
- Multi-provider architecture with automatic fallback
- Tested with five East African cities and continents
- Implemented reverse geocoding for coordinates to make places turn into names

### 3: Load Balancer Configuration
**Problem**: Health checks were failing intermittently, causing server removal.

**Solution**:
- Adjusted health check intervals (2 seconds)
- Configured rise/fall thresholds (rise=2, fall=3)
- Added proper health check endpoint
- Implemented comprehensive logging

### 4: User Experience on Mobile
**Problem**: Initial design not being mobile-friendly; buttons were small, and hard for the text to read.

**Solution**:
- Implemented a web-based application responsive on various devices
- Apply CSS styling, Flexbox, and Grid
- Tested on multiple device sizes
- Added touch-friendly button sizing (minimum 44px)

### 5: API Key Security
**Problem**: API keys' security purpose from being exposed in a public repository.

**Solution**:
- Created `.gitignore` to exclude configuration files
- Used environment variables for deployment
- Documented security practices
- Implemented key rotation policy

### 6: Error Handling
**Problem**: Users are experiencing confusing errors when APIs fail.

**Solution**:
- Implemented a comprehensive try-catch block throughout the user-friendly code
- User-friendly error messages with recovery steps
- Automatic fallback to cached data
- Graceful degradation when services are unavailable

### 7: Data Presentation
**Problem**: The initial data interface was being overwhelmed, making users unable to find accurate information.

**Solution**:
- Implemented pagination (9 facilities per page)
- Added sorting and filtering options
- Created card-based layout for clarity
- Included search-within-results feature

### 8: Performance Optimization
**Problem**: Page loaded slowly due to all assets and API calls.

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
- Application database for storing data

### Performance Improvements
- Further caching optimizations
- Enhanced load balancing algorithms
- CDN integration for static assets
- Continuous monitoring and alerting systems

## License
This project is open source. API usage subject to respective provider terms.