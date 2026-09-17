# Deployment Evidence

Fill this in as you go. Paste real output, not descriptions of output. A TA reads this
file with you at recitation.

## 1. Deployed URL and instance id

```
-----------------------------------------------------------------------
|                           DescribeStacks                            |
+------------+--------------------------------------------------------+
|  InstanceId|  i-085940c9e09a66d28                                   |
|  ServiceUrl|  http://ec2-54-147-8-96.compute-1.amazonaws.com:8080   |
+------------+--------------------------------------------------------+
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
- Glue: the LabInstanceProfile IAM role enables SSM shell access with no key pair, and a UserData-scheduled shutdown after 4 hours prevents runaway costs.

## 4. Scenario 2 diagnosis

**The failing curl** (command and output):

```

```

**The log line that told you what was wrong:**

```

```

**What was wrong, and the fix you applied:**

<!-- One or two sentences. Say what you changed and where you changed it. -->

**The healthy curl after the fix:**

```

```

## 5. Teardown proof

Paste the delete output, or describe the console evidence that the resources are gone.

```

```
