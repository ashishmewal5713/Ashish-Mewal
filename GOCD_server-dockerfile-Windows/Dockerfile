# Use the official GoCD server image
FROM gocd/gocd-server:v25.1.0

# Switch to root user
USER root

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive \
    TZ=America/New_York

# Configure the time zone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install dependencies using apk (for Alpine Linux)
RUN apk update && \
    apk add --no-cache \
      tzdata jq wget curl unzip zip git \
      openssh-client openssh-server \
      bash && \
    rm -rf /var/cache/apk/*

# Install AWS CLI v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscli.zip" && \
    unzip awscli.zip && \
    ./aws/install

# Ensure GoCD server service starts
ENTRYPOINT ["/docker-entrypoint.sh"]
