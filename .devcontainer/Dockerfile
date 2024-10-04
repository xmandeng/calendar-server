# Use the official Nginx image as the base image
FROM nginx:latest

# Build arguments for user configuration
ARG USER_UID=1000
ARG USER_GID=1000
ARG USER_PASSWORD=vscode

# Install necessary packages
RUN apt-get update && apt-get install -y \
    sudo \
    git \
    libcap2-bin \
    ca-certificates \
    openssh-client \
    less \
    iproute2 \
    procps \
    && rm -rf /var/lib/apt/lists/*

# Create a group and user
RUN groupadd --gid $USER_GID vscode \
    && useradd --uid $USER_UID --gid $USER_GID --shell /bin/bash --create-home vscode \
    && echo "vscode:$USER_PASSWORD" | chpasswd \
    && usermod -aG sudo vscode \
    && echo "vscode ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/vscode \
    && chmod 0440 /etc/sudoers.d/vscode

# Remove default Nginx configuration
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/conf.d/

# Adjust permissions
RUN chown -R vscode:vscode /var/cache/nginx /var/log/nginx /var/run /usr/share/nginx/html

# Allow Nginx to bind to privileged ports (e.g., port 80)
RUN setcap 'cap_net_bind_service=+ep' /usr/sbin/nginx

# Switch to the vscode user
USER vscode

# Expose port 80
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
