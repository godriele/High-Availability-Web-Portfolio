# High-Availability-Web-Portfolio

**ACIT-3475: Web Server Administrator**  
**Project: High-Availability Personal/Professional Portfolio with OAuth and GitHub Contributions Display**

## Overview
This project is a **highly available personal/professional portfolio website** that supports secure editing via **Google OAuth 2.0**, load balancing via **HAProxy**, and integrates a **GitHub contributions calendar** using a CDN-powered widget.

Built with scalability, security, and usability in mind, the project showcases student work, tech stack, and contributions, while demonstrating a solid grasp of modern DevOps and full-stack development practices.

---

##  Key Objectives

1. **OAuth Integration** – Secure login/editing using Google Cloud's OAuth 2.0.
2. **Load Balancing** – HAProxy distributes traffic between multiple Nginx servers.
3. **Nginx Configuration** – Web server and reverse proxy functionality.
4. **GitHub Contributions Widget** – Display GitHub activity via CDN integration.
5. **High Availability** – Fault-tolerant, scalable, and secure setup.

---

## Features & Implementation Steps

### 1. Frontend
- Developed with **HTML**, **CSS**, **JavaScript**, and **EJS** templating.
- GitHub calendar widget embedded and styled to match the theme.
- Authenticated users can edit projects or add new ones.

### 2. Backend
- **Express.js** backend with EJS for server-rendered content.
- Routes for authentication, project management, and user profile.

### 3. OAuth Implementation
- Used **Google Cloud Console** to set up OAuth credentials.
- Configured callback URLs and scopes.
- Only authenticated users can access `/profile/add-project`.

### 4. Nginx Configuration
- Configured multiple **Nginx** instances listening on different ports.
- Each instance reverse proxies requests to the Node.js backend.
- SSL and Gzip enabled for better performance and security.
- Config-file example: "/etc/nginx/sites-available/default"
  ```
  server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/html;

        server_name project2.com;

      location / {

                proxy_pass http://localhost:8000;  # Assuming HAProxy is running on port 8000
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

  upstream app_backned {
        server 127.0.0.1:3000;
        server 127.0.0.1:3002;
  }



### 5. HAProxy Load Balancing
- **HAProxy** listens on port 80 and forwards traffic to Nginx instances.
- Health checks ensure traffic goes only to available backends.
- Config file example: "/etc/haproxy/haproxy.cfg"
  - Frontend
    ```
    frontend http_front
    bind *:8000  # Frontend listens on port 80 (adjust if you're using a different port)
    default_backend http_back
    ```
  - Backend
    ```
    backend http_back
    balance roundrobin  # Load balancing method (you can try leastconn as well)
    option httpchk GET /  # Health check endpoint, to make sure both apps are responding
    server app 127.0.0.1:3000 check inter 2000 fall 3 rise 2  # Backend on port 3000
    server app2 127.0.0.1:3002 check inter 2000 fall 3 rise 2  # Backend on port 3002


    ```


### 6. GitHub Contributions Widget Integration

To visually showcase recent GitHub activity on the portfolio site, a **GitHub Contribution Calendar Widget** was integrated using a CDN for optimal performance and reliability.

#### Integration Steps:
- CDN Script Import
  The widget is loaded via jsDelivr CDN, which ensures fast and globally distributed access to the script:
```
<script type="module" defer src="https://cdn.jsdelivr.net/gh/imananoosheh/github-contributions-fetch@latest/github_calendar_widget.js"></script>
Container Setup
```

- A <div> container is added to the webpage to hold the calendar. The container's id is set to "calendar-component", and the GitHub username is passed as an attribute:
```
<div id="calendar-component" username="<your-github-username>"></div>
```

##### Customization (Optional)
You can customize the appearance of the calendar by adding optional attributes to the <div> element. For example, to set the theme color of the calendar:
```
<div id="calendar-component" username="<your-github-username>" theme-color="#4285f4"></div>
```

##### Available customization options include:

- theme-color: Sets the main color of the calendar.

- background-color: Sets the background color of the calendar.


#### CDN Benefits for Performance
By utilizing the CDN (jsDelivr), the GitHub Contributions Widget is served from multiple edge locations across the globe, significantly improving load times and reducing latency. With the widget cached at these locations, the response time for users accessing the portfolio website is faster, as they fetch the widget script from the nearest server.

The CDN also ensures high availability, as the widget script is distributed across multiple servers, allowing for fault tolerance and uninterrupted access even during high traffic periods.

