# K8-Exercise
Kubernetes take home exercise for Thinkon.

# Exercise Description
1.) We want to deploy two containers that scale independently from one another.

    Container 1: This container runs code that runs a small API that returns users from a database.
    Container 2: This container runs code that runs a small API that returns shifts from a database.

2.) For the best user experience auto scale this service when the average CPU reaches 70%.

3.) Ensure the deployment can handle rolling deployments and rollbacks.

4.) Your development team should not be able to run certain commands on your k8s cluster, but you want them to be able to deploy and roll back. What types of IAM controls do you put in place?


# Approach
For this task, I have made two deployments each containers on GCP, and a horizontal pod autoscaler for each.


# Containers
Both the containers used for this exercise are Alpine containers since I used it only for testing the Autoscaling.

# Namespaces
I have used two namespaces: **staging** and **production**.


# Deployments
The two deployments used are almost identical. The idea beneath using two different deployments is allowing them to scale independently.

The containers deployment strategy is: RollingUpdate. It adds gradually one pod at a time while removing only one each time, which enables more availability in case of failover.

The minimum available resources should be 2000 millicore for cpu and 3000Mi for memory. And the resources starts throttling at 5000m for cpu and 6000Mi for memory.


# Auto Scaler
For scaling, I have used horizontal scaling with K8's HorizontalPodScaling found in the **hpa.yaml** file.

I have assumed that **first-deployment** will have more requests so it is aided with a minimum number of replicas of 2 while the maximum is 8 which is reached when the cpu avarage utilitzation of all pods is above 70%. 

The other service: **second-deployment** will have less requests so it requires less resources with a minimum of 2 and a maximum of 4 pods when the cpu avarage utilitzation is more than 70%.

# Day and night
Since the Horizontal Autoscaler is able to reduce the number of pods running and increase it when needed, the pods will be at their minimum at night and get increased at day when the cpu utilization starts rising.

If in night the number of desired pods should be less than the minimum number used in the deployment, I would make a cronjob that lowers the number of pods after a particular hour in night until the morning by applying an hpa.

# Bonus Questions
·    How would you apply the configs to multiple environments (staging vs production)?
As metioned in the Namespaces section, I have used two namespaces: **staging** and **production**.

I think for small scale projects dividing the environments by namespaces would be more sufficient since having two different clusters would be more costly. This requires more attention by the DevOps engineer responsible for deploying.

For large scale projects where resources are more abundant, I would have two distinguished clusters for each environment to reduce the possibility of getting errors. 

·    How would you auto-scale the deployment based on network latency instead of CPU?

In this exercise I have only used deployments with **alpine**. Whereas in a complete project where deployments have related services, if network latency occurs, based on custom network metrics, for example the number of http requests, I would increase the number of the replicas of the ingress controllers used in order to cope with the high number of requests. 

# IAM
Since I have used Google Cloud Platform, I would use the IAM **Cloud Deploy Developer** which gives "Permission to manage deployment configuration without permission to access operational resources, such as targets." according to GCP.


# Additional Notes
Actually, in this exercise, it was my first time using GCP since my company uses only on-premise servers. It was a really fun experience exploring it.


