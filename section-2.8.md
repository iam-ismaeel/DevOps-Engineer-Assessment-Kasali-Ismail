Question
Write a script to restart the Laravel backend service if CPU usage exceeds 80%.


To start with,lets start with a laravel backend pod running in a kubernetes cluster,
the deployment resource is shown below.




1. Deployment YAML (Including CPU and Memory Requests and Limits)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-backend
  labels:
    app: laravel-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: laravel-backend
  template:
    metadata:
      labels:
        app: laravel-backend
    spec:
      containers:
      - name: laravel-container
        image: my-laravel-image:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "500m"      
            memory: "512Mi"   
          limits:
            cpu: "1"          
            memory: "1Gi"     
```

Also,we need to set an HPA  to scale the pod whenenver threshold s reached
2. Horizontal Pod Autoscaler (HPA) YAML
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: laravel-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: laravel-backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80 # Scale when CPU exceeds 80%
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 70 # Scale when Memory exceeds 70%
```

3. A Bash Script to Restart the Laravel Backend If CPU Exceeds 80%

Here’s a script to monitor CPU usage and restart the Laravel service if the threshold is crossed. This script uses kubectl and assumes you have access to the Kubernetes cluster.
```
#!/bin/bash

# Variables
DEPLOYMENT_NAME="laravel-backend"
NAMESPACE="default"
THRESHOLD=80

# Function to get CPU usage for the deployment
get_cpu_usage() {
  kubectl top pod -n $NAMESPACE --selector=app=$DEPLOYMENT_NAME --no-headers | \
    awk '{sum += $2} END {if (NR > 0) print sum / NR; else print 0}'
}

# Monitor and restart if needed
while true; do
  CPU_USAGE=$(get_cpu_usage)

  if [ -n "$CPU_USAGE" ] && (( ${CPU_USAGE%.*} > THRESHOLD )); then
    echo "CPU usage exceeded $THRESHOLD% (Current: $CPU_USAGE%). Restarting deployment..."
    kubectl rollout restart deployment/$DEPLOYMENT_NAME -n $NAMESPACE
  else
    echo "CPU usage is normal (Current: $CPU_USAGE%)."
  fi

  # Sleep for 60 seconds before checking again
  sleep 60
done
```

Explanation

1. Deployment YAML:
Configures CPU and memory requests and limits for the Laravel backend service.

2. HPA YAML:
Sets up autoscaling based on CPU (80%) and memory (70%) utilization.

3. Bash Script:
Uses kubectl top to fetch CPU usage of pods matching the app=laravel-backend label.
Restarts the deployment if CPU usage exceeds 80%.
Runs continuously, checking usage every 60 seconds.


Run the Script

1. Save the script to a file, e.g., restart_laravel.sh.

2. Make it executable:
chmod +x restart_laravel.sh

3. Run the script:
./restart_laravel.sh
