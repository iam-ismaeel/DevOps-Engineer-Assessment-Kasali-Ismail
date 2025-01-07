Question
Describe a time you improved the performance of an infrastructure system. What
challenges did you face?



Optimizing Kubernetes-based Microservices Deployment

The infrastructure supported multiple Node.js microservices deployed on a Kubernetes cluster. Over time, the system experienced degraded performance under high traffic:

Increased API response times.
Frequent resource contention.
Random service crashes during peak hours.


Actions Taken

1. Analyzed Metrics and Identified Bottlenecks
Used Prometheus and Grafana to monitor CPU, memory usage, and request latencies.
Found that certain services were experiencing CPU throttling due to insufficient resource allocation.

2. Implemented Horizontal Pod Autoscaling (HPA)
Configured HPA in Kubernetes to scale pods dynamically based on CPU and memory utilization.

Challenges:

Determining the right threshold values for autoscaling.
Preventing over-provisioning and minimizing costs.

3. Implemented Caching

Added Redis as a caching layer for frequently accessed data, reducing load on the database.

Challenges:

Identifying data suitable for caching without causing consistency issues.
Implementing cache invalidation strategies to ensure data accuracy.

4. Improved Service-to-Service Communication
Switched from REST-based communication to gRPC for certain services, reducing latency and payload size.

Challenges:
Refactoring services to support gRPC while ensuring backward compatibility for external clients.

5. Optimized CI/CD Pipelines

The deployment pipeline was slow, often delaying fixes.
Introduced parallel builds, Docker layer caching, and reduced unnecessary build steps.

Challenges:
Ensuring the pipeline changes didn’t introduce breaking changes or vulnerabilities.

Challenges Faced

1. Balancing Performance and Costs:

Autoscaling increased infrastructure costs. To address this, I optimized resource requests/limits and scaled down non-critical services during off-peak hours.

2. Team Coordination:

Multiple teams owned different services, and changes required extensive communication and collaboration to avoid disruptions.

3. Testing Under Load:

Simulating production-like traffic for load testing without affecting the live system was challenging. Addressed this by setting up a staging environment with traffic simulation tools like k6.


4. Rolling Out Changes Gradually:
Each optimization required careful rollout and monitoring to ensure no regressions or disruptions.




Results

Reduced API response times by 35%.
Improved system stability under high traffic.
Achieved cost savings by 20% through efficient resource allocation and caching.
Enhanced developer productivity with faster deployments.
