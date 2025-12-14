# This Dockerfile defines the process of building a Docker image.
# It contains instructions to install the Python runtime environment...
# ...and prepare the application for execution inside a container.

# Clear version without commentary 
FROM python:3-alpine
WORKDIR /workspace
COPY . /workspace
RUN apk add --no-cache build-base linux-headers
RUN pip install --no-cache-dir -r requ.txt
RUN mkdir -p /workspace/results
EXPOSE 8080
ENTRYPOINT ["python", "estimation-π.py"]