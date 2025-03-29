Image Search Engine
Overview
The Image Search Engine is a web application that allows users to search for high-quality images using keywords. It leverages the Unsplash API to fetch and display images dynamically, providing a clean and responsive user interface built with HTML, CSS, and JavaScript. Users can search for images, view results in a grid layout, and load more images using a "Show More" button. Each image links to its source on Unsplash for further exploration.
This project fulfills the assignment requirements by integrating an external API (Unsplash) to deliver meaningful value—enabling users to discover and explore images based on their interests—while avoiding gimmicky functionality.

Part One: Local Implementation
Features
Search Functionality: Users can input a keyword to search for images.
Dynamic Results: Displays a grid of image results with hover effects and links to the original source.
Pagination: A "Show More" button loads additional results without refreshing the page.
Responsive Design: Adapts to various screen sizes (desktop, tablet, mobile).
User-Friendly Interface: Intuitive layout with a clean, modern design.
Technologies Used
HTML: Structure of the web application.
CSS: Styling with a custom design (responsive grid, hover effects, etc.).
JavaScript: Handles API requests, DOM manipulation, and event listeners.
Unsplash API: External API for fetching image data.
Prerequisites
A modern web browser (e.g., Chrome, Firefox, Edge).
An Unsplash API key (free tier available at Unsplash Developers).
A local development environment (e.g., VS Code with Live Server extension).
Setup Instructions
Clone or Download the Project
Copy the provided HTML code into a file named index.html.
Obtain an Unsplash API Key
Sign up for a free account at Unsplash Developers.
Create a new application to receive an Access Key.
Replace the accessKey value in the <script> section of index.html with your own key:
 javascript
CollapseWrapCopy
const accessKey = "YOUR_ACCESS_KEY_HERE";


Run the Application Locally
Open index.html in a web browser directly, or use a local server (e.g., Live Server in VS Code) for a better development experience.
Enter a search term (e.g., "nature") in the input field and click "Search" to see results.
API Details
Endpoint: https://api.unsplash.com/search/photos
Parameters: page, query, client_id
Rate Limit: 50 requests/hour (free tier). Refer to the Unsplash API Documentation for more details.
Usage
Type a keyword into the search bar (e.g., "mountains", "animals").
Press "Search" or hit Enter to fetch images.
Hover over an image to see a subtle animation.
Click an image title to visit its Unsplash page.
Click "Show More" to load additional results.

Part Two: Deployment
Deployment Overview
The application is deployed on two standard web servers (Web01 and Web02) with a load balancer (Lb01) distributing traffic between them. This setup ensures reliability and scalability, allowing users to access the Image Search Engine seamlessly via the load balancer's address.
Prerequisites
Access to Web01, Web02, and Lb01 servers.
SSH access and basic Linux command-line knowledge.
A web server software installed (e.g., Nginx or Apache) on Web01 and Web02.
A load balancer software installed (e.g., HAProxy or Nginx) on Lb01.
Deployment Instructions
Step 1: Deploy the Application on Web Servers
Prepare the Files
Ensure index.html contains your Unsplash API key.
No additional files are needed since all CSS and JavaScript are embedded.
Upload to Web Servers
Use SCP or SFTP to copy index.html to both Web01 and Web02:
 bash
CollapseWrapCopy
scp index.html user@web01:/var/www/html/
scp index.html user@web02:/var/www/html/


Replace user@web01 and /var/www/html/ with your actual username and web directory path.
Configure Web Servers
Install Nginx (if not already installed):
 bash
CollapseWrapCopy
sudo apt update
sudo apt install nginx


Ensure Nginx serves the file from /var/www/html/.
Start/restart Nginx:
 bash
CollapseWrapCopy
sudo systemctl restart nginx


Verify the site loads by accessing http://<Web01_IP> and http://<Web02_IP> in a browser.
Step 2: Configure the Load Balancer
Install HAProxy on Lb01
Install HAProxy:
 bash
CollapseWrapCopy
sudo apt update
sudo apt install haproxy


Edit HAProxy Configuration
Open the configuration file:
 bash
CollapseWrapCopy
sudo nano /etc/haproxy/haproxy.cfg


Add the following at the end of the file (adjust IPs as needed):
 plaintext
CollapseWrapCopy
frontend http_front
   bind *:80
   mode http
   default_backend http_back

backend http_back
   mode http
   balance roundrobin
   server web01 <Web01_IP>:80 check
   server web02 <Web02_IP>:80 check


Replace <Web01_IP> and <Web02_IP> with the actual IP addresses of Web01 and Web02.
Restart HAProxy
Apply the changes:
 bash
CollapseWrapCopy
sudo systemctl restart haproxy


Test the Deployment
Access the application via the load balancer’s IP (e.g., http://<Lb01_IP>).
Refresh the page multiple times to confirm traffic is distributed between Web01 and Web02.
Use server logs (e.g., /var/log/nginx/access.log) on Web01 and Web02 to verify load balancing.
Verification
The application should load and function identically to the local version.
Search functionality should work without exceeding the Unsplash API rate limit.
The load balancer should alternate requests between Web01 and Web02 (round-robin).

Notes
API Rate Limits: The free Unsplash API tier is limited to 50 requests/hour. For production use, consider requesting a higher limit or caching results.
Security: Ensure your API key is not exposed in public repositories. In a production environment, use environment variables or a backend proxy.
Scalability: The current setup uses two servers, but additional servers can be added to the haproxy.cfg backend for further scaling.
Troubleshooting
API Errors: Check the browser console for Unsplash API response errors (e.g., invalid key, rate limit exceeded).
Server Issues: Verify Nginx and HAProxy services are running (sudo systemctl status nginx and sudo systemctl status haproxy).
Load Balancer: Ensure Web01 and Web02 are reachable from Lb01 (test with ping or curl).
