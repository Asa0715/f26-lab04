# Deployment Evidence

Fill this in as you go. Paste real output, not descriptions of output. A TA reads this
file with you at recitation.

## 1. Deployed URL and instance id

### Milestone 1 record

```
-----------------------------------------------------------------------
|                           DescribeStacks                            |
+------------+--------------------------------------------------------+
|  InstanceId|  i-085940c9e09a66d28                                   |
|  ServiceUrl|  http://ec2-54-147-8-96.compute-1.amazonaws.com:8080   |
+------------+--------------------------------------------------------+
```
### Milestone 2 record
```
------------------------------------------------------------------------
|                            DescribeStacks                            |
+------------+---------------------------------------------------------+
|  InstanceId|  i-01f7892c318be1e55                                    |
|  ServiceUrl|  http://ec2-34-229-9-148.compute-1.amazonaws.com:8080   |
+------------+---------------------------------------------------------+
```
**After fix:**
```
-------------------------------------------------------------------------
|                            DescribeStacks                             |
+------------+----------------------------------------------------------+
|  InstanceId|  i-04fc26512bb6c895e                                     |
|  ServiceUrl|  http://ec2-54-227-53-189.compute-1.amazonaws.com:8080   |
+------------+----------------------------------------------------------+
```

<!-- The ServiceUrl and InstanceId outputs. Paste both here every time
describe-stacks prints them, for the healthy deploy and for scenario 2. Both
change on every recreate, and you will need them for curls and sessions. -->

## 2. External health check

Run the check from your own machine, not from the instance. Paste the command and the
response.

```
curl http://ec2-54-147-8-96.compute-1.amazonaws.com:8080/api/health
{"status":"ok"}%                
```

## 3. What the template created

Three or four sentences, your own words. What compute, what network access, and what
glue made the service start.

- Compute: one t3.micro EC2 instance (Amazon Linux 2023) whose UserData script installs Docker and runs the lab04-service container on port 8080.
- Network: a security group opening only port 8080 (health check) and port 22 (SSH fallback) inbound; outbound left at the default so the instance can pull the image.
- Glue: the instance's UserData script (infra/template.yaml:80-108）) runs at first boot: it installs and starts Docker, then runs the lab04-service image with docker run -p 8080:8080 -e PORT=..., which is what actually starts the service.

## 4. Scenario 2 diagnosis

**The failing curl** (command and output):

```
curl http://ec2-34-229-9-148.compute-1.amazonaws.com:8080/api/health
curl: (7) Failed to connect to ec2-34-229-9-148.compute-1.amazonaws.com port 8080 after 49 ms: Couldn't connect to server
```

**The log line that told you what was wrong:**

```
aws ssm start-session --target i-01f7892c318be1e55

Starting session with SessionId: user5412204=Shuangxueer_Zhang-gy9z7xotyxfz4vk9v4p2dzt6ke
sh-5.2$ sudo docker ps
sudo docker logs lab04-service
CONTAINER ID   IMAGE                                     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
7f71e3f361e7   ghcr.io/cmu-17-214/lab04-service:latest   "/__cacert_entrypoin…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   lab04-service
lab04-service listening on 9090
```

**What was wrong, and the fix you applied:**

The PortOverride parameter in params-scenario2.json was set to 9090, which overrode the container's PORT environment variable and made the service process listen on 9090, while the Docker port mapping and security group still forwarded traffic to 8080 (unchanged). The fix was infrastructural: I deleted the broken stack and recreated it with params-healthy.json, where PortOverride is empty, so the service process falls back to listening on ServicePort (8080) and now matches the unchanged port mapping.

**The healthy curl after the fix:**

```
curl http://ec2-54-227-53-189.compute-1.amazonaws.com:8080/api/health
{"status":"ok"}%                       
```

## 5. Teardown proof

Paste the delete output, or describe the console evidence that the resources are gone.

```

```
