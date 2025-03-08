FROM gocd/gocd-agent-ubuntu-20.04:v25.1.0
 
# Switch to root so we can install packages
USER root
 
# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive \
    TZ=America/New_York \
    GO_SERVER_URL="http://gocd-server:8153/go"
    
# Configure the time zone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
 
# Install dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      tzdata jq wget curl unzip zip git ruby ruby-dev bundler \
      openssh-client openssh-server proxychains \
      build-essential libssl-dev libbz2-dev libreadline-dev libsqlite3-dev \
      llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev \
      libffi-dev liblzma-dev libsm6 libnss3-dev \
      rsync tar opensc software-properties-common make && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
 
# Add deadsnakes PPA and install Python 3.11
RUN add-apt-repository ppa:deadsnakes/ppa -y && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
      python3.11 python3.11-venv python3.11-dev python3.11-distutils && \
    rm -rf /var/lib/apt/lists/*
 
# Update default Python to 3.11
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1 && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 2 && \
    update-alternatives --set python3 /usr/bin/python3.11
 
# 🔹 Create a soft link: python3 → python
RUN ln -sf /usr/bin/python3 /usr/bin/python
 
# Upgrade pip for Python 3.11
RUN python3 -m ensurepip && python3 -m pip install --upgrade pip
 
# Install AWS CLI v2
RUN apt-get update && apt-get install -y curl unzip  
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscli.zip" && \
    unzip awscli.zip && \
    ./aws/install  
 
# Accept Chef license
ENV CHEF_LICENSE=accept
 
# Install Chef CLI (Minimal instead of full Workstation)
RUN curl -L https://www.chef.io/chef/install.sh | bash -s -- -P chef -v 17 && \
    ln -s /opt/chef/bin/chef /usr/local/bin/chef
 
# Copy requirements.txt first for caching
COPY requirements.txt /tmp/
 
# Switch to GoCD agent user
USER go
 
# Install Python dependencies
RUN python -m pip install --no-cache-dir -r /tmp/requirements.txt
 
# Ensure GoCD agent service starts
ENTRYPOINT ["/docker-entrypoint.sh"]
