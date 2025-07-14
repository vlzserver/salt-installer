#!/bin/bash
set -e

# Detect CentOS version
CENTOS_VERSION=$(rpm -E %{rhel})
if [[ "$CENTOS_VERSION" != "7" && "$CENTOS_VERSION" != "8" ]]; then
  echo "❌ Unsupported CentOS version: $CENTOS_VERSION"
  exit 1
fi

# Set download base URL
BASE_URL="https://mirrors.aliyun.com/saltstack/salt/py3/redhat/${CENTOS_VERSION}/x86_64/minor/3005.1-4"

# Create download directory
mkdir -p ~/salt3005_rpms
cd ~/salt3005_rpms || exit 1

# Download available RPMs (salt + salt-minion only)
wget -q ${BASE_URL}/salt-3005.1-4.el${CENTOS_VERSION}.x86_64.rpm
wget -q ${BASE_URL}/salt-minion-3005.1-4.el${CENTOS_VERSION}.x86_64.rpm

# Install the RPMs
sudo yum install -y ./salt-3005.1-4.el${CENTOS_VERSION}.x86_64.rpm ./salt-minion-3005.1-4.el${CENTOS_VERSION}.x86_64.rpm --nogpgcheck

# Start and enable salt-minion
sudo systemctl enable --now salt-minion

# Show version
salt-minion --version
