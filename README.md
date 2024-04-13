# Flask Application on Google Cloud Kubernetes

This guide explains how to deploy a Flask application on Google Cloud Kubernetes.

## Prerequisites

- Google Cloud account
- Google Cloud SDK installed
- kubectl installed
- Docker installed

## Step 1: Create a Flask App

Create a new directory for your project and navigate into it:

```bash
mkdir my-flask-app
cd my-flask-app
```

Create a new Python virtual environment and activate it:

```bash
python -m venv venv
source venv/bin/activate
```

Install Flask:

```bash
pip install Flask
```

Create a new file named `app.py` with the following contents:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

## Step 2: Containerize the Flask App

Create a `Dockerfile` in the same directory:

```Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 8080 available to the world outside this container
EXPOSE 8080

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Build your Docker image:

```bash
docker build -t my-flask-app .
```

## Step 3: Deploy to Google Cloud Kubernetes

Create a Kubernetes cluster:

```bash
gcloud container clusters create my-cluster --num-nodes=3
```

Configure `kubectl` to use the cluster:

```bash
gcloud container clusters get-credentials my-cluster
```

Create a deployment configuration `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: gcr.io/[PROJECT-ID]/my-flask-app:latest
        ports:
        - containerPort: 8080
```

Create a Kubernetes deployment:

```bash
kubectl apply -f deployment.yaml
```

Expose the deployment:

```bash
kubectl expose deployment flask-deployment --type=LoadBalancer --port=8080
```

## Step 4: Access Your App

Retrieve the external IP address of your service:

```bash
kubectl get service
```

Open a web browser and navigate to the external IP address at port 8080.

## Conclusion

Your Flask application is now running on Google Cloud Kubernetes!
