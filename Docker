# Use the official Nginx image from the Docker Hub
FROM nginx:1.26.1

# Copy custom configuration file from the src directory
COPY src/nginx.conf /etc/nginx/nginx.conf

# Copy static website files to the Nginx web root
COPY src/index.html /usr/share/nginx/html/index.html