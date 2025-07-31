# 💊 Smart Drug Info App
A lightweight web application that uses the FDA Drug Label API to provide verified information about medications. This app is containerized using Docker and deployed with a HAProxy load balancer.

📦 Features
✅ User-friendly web interface for drug lookup

✅ Personalized user experience based on basic health data

✅ Backend powered by the FDA API

✅ Load balanced across multiple Docker containers using HAProxy

🏗️ Project Structure
web_infra_lab/
│
├── Dockerfile
├── haproxy.cfg
├── index.html
├── style.css
├── script.js
├── app.py (if using Flask for backend logic)
└── ...
🚀 Local Deployment (with Docker)
1. Clone the Repository
```bash
git clone https://github.com/Nzabinesha/drug-info-app.git
```
cd drug-info-app
3. Build Docker Image

```bash
docker build -t nzabineshamerci/drug-info-app:v1 .
```
3. Create Docker Network

```bash
docker network create webnet
```

4. Run App Containers

```bash
docker run -d --name app-web-01 --network webnet nzabineshamerci/drug-info-app:v1
docker run -d --name app-web-02 --network webnet nzabineshamerci/drug-info-app:v1
```
5. Create HAProxy Config (haproxy.cfg)
```cfg
global
    daemon
    maxconn 256

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend http-in
    bind *:80
    default_backend servers

backend servers
    balance roundrobin
    server app1 app-web-01:80 check
    server app2 app-web-02:80 check
````
6. Run Load Balancer Container
```bash
docker run -d --name lb-01 \
  --network webnet \
  -p 80:80 \
  -v $(pwd)/haproxy.cfg:/etc/haproxy/haproxy.cfg:ro \
  haproxy:latest haproxy -f /etc/haproxy/haproxy.cfg
```
✅ Verifying Load Balancing
Edit both containers’ index.html to show which instance served the request:


```bash
docker exec -it app-web-01 sh -c 'echo "<p style=\"color: green;\">Served by: app-web-01</p>" >> /usr/share/nginx/html/index.html'

docker exec -it app-web-02 sh -c 'echo "<p style=\"color: blue;\">Served by: app-web-02</p>" >> /usr/share/nginx/html/index.html'
```
Use curl multiple times:

```bash
curl -s http://localhost | grep "Served by"
You should see alternating outputs from both containers.
```

🔐 Notes
This app currently runs on localhost. You can map it to a public IP or domain if needed.

Ensure Docker is installed and running on your host system.

The app assumes your frontend is served by nginx or another lightweight web server.

🔐 Notes
This app currently runs on localhost via Docker.

To expose externally, you will need a public IP, domain, or a cloud server with Docker installed.

Make sure Docker is installed and running on your host system.

The app frontend is served by nginx inside the container.

FDA Drug API requires no API key but always check API limits and terms.

⚠️ Limitations & Known Issues
The app is currently only accessible on localhost (127.0.0.1).

No HTTPS / SSL termination configured on HAProxy by default.

No persistence layer; data is fetched live from the API on each request.

Some API downtime or rate limiting from FDA API may affect responses.

No user authentication implemented (optional bonus).

📸 Demo Screenshot

(Add your actual screenshot file here)

📌 Author
Merci Nzabinesha
Student at African Leadership University
Medicine and Software Engineering
