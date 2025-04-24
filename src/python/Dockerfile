FROM python:3.12-slim

WORKDIR /app

# Copy requirements first for better caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Command to run when container starts
CMD ["python", "-m", "azure_agent_mcp_server"]

# For Azure Container Apps
EXPOSE 8000
