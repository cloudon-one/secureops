FROM hashicorp/terraform:1.7.0 AS terraform
FROM aquasec/tfsec:v1.28 AS tfsec
FROM python:3.10-slim

# Copy tools from their respective images
COPY --from=terraform /bin/terraform /usr/local/bin/
COPY --from=tfsec /usr/bin/tfsec /usr/local/bin/

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    make \
    gcc \
    jq \
    wget \
    unzip \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install Python packages
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir \
    pre-commit \
    checkov

# Install TFLint
RUN curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash

# Install terraform-docs
RUN curl -sSLo ./terraform-docs.tar.gz https://terraform-docs.io/dl/v0.16.0/terraform-docs-v0.16.0-$(uname)-amd64.tar.gz && \
    tar -xzf terraform-docs.tar.gz && \
    chmod +x terraform-docs && \
    mv terraform-docs /usr/local/bin/ && \
    rm terraform-docs.tar.gz

# Install Infracost directly
RUN curl -fsSL https://raw.githubusercontent.com/infracost/infracost/master/scripts/install.sh | sh && \
    infracost --version

# Verify installations
RUN terraform version && \
    tfsec --version && \
    infracost --version && \
    tflint --version && \
    terraform-docs --version

WORKDIR /workspace
CMD ["pre-commit", "run", "--all-files"]
