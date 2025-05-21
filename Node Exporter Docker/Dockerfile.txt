FROM ubuntu:latest

# Avoid interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary tools
RUN apt-get update && \
    apt-get install -y curl wget nano && \
    rm -rf /var/lib/apt/lists/*

# Add prometheus user and group
RUN groupadd --system prometheus && \
    useradd -s /sbin/nologin --system -g prometheus prometheus

# Create directory for node_exporter
RUN mkdir -p /var/lib/node

# Copy node_exporter binary (you must place this in the build context)
COPY node_exporter /var/lib/node/

# Set permissions
RUN chown -R prometheus:prometheus /var/lib/node && \
    chmod -R 775 /var/lib/node

# Expose node_exporter default port
EXPOSE 9100

# Set user
USER prometheus

# Run node_exporter directly (no systemd)
CMD ["/var/lib/node/node_exporter"]
