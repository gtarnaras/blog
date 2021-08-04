---
layout: post
title: How to add healthchecks to your docker images
subtitle: >-
  Check instantly whether your application is actually running.
date: '2021-02-17T00:49:31.531Z'
thumbnail-img: /assets/img/1__0gvBT2vOAAHl1LuBHWS4Vw.png
categories: []
keywords: []
tags: [Docker, DevOps, Monitoring, Dockerfiles]
readtime: true
---

<p align="center">
  <img src="/assets/img/1__0gvBT2vOAAHl1LuBHWS4Vw.png">
</p>

How can you make sure that your dockerised application actually works, without having to do anything about it? The fast answer is check the app’s endpoint but the fastest way is to add a Docker healthcheck. Healthchecks are also used by docker-compose to replace failed nodes etc. but let’s keep it simple. Now, I am going to try and persuade you to start using healthchecks whenever you are building a new application (and I will try to do this in less than 5 minutes… so the clock is ticking).

**I want to test it right now:**

Feel free to clone the repo below and just follow the README in order to give it a try:

[github-repo](https://github.com/gtarnaras/docker-http-server.git)

**I just want a simple healthcheck:**

The healthcheck is actually part of the Dockerfile and it could be a custom script, command etc. that checks something for you. According to the check’s return code your docker app will be marked as Healthy or Unhealthy. Here is a simple healthcheck written in python:

```
import sys, requests

endpoint\_check = "http://0.0.0.0:8080"

try:

    resp = requests.head(endpoint\_check)

    print(resp.status\_code)

    sys.exit(0) # return code 0 = healthy

except requests.ConnectionError:

    print("failed to connect")

    sys.exit(1) # return code 1 = unhealthy
```

**I now want to test it with my Dockerfile:**

In the Dockerfile below our healthcheck is the simple python script above. It is checking the endpoint of our application and it returns 0 (success) or 1 (failure):

#### Dockerfile

```
FROM python:latest

RUN pip install requests

COPY index.html /
COPY healthcheck.py /

HEALTHCHECK --interval=12s --timeout=12s --start-period=10s CMD python /healthcheck.py

CMD python -m http.server 8080

EXPOSE 8080
```

It is worth explaining the flags we’re passing in the HEALTHCHECK instruction a bit more:

*   **interval**: The time after the first check will run and how often it will run after it.
*   **timeout:** If a healthcheck takes longer than this time to complete, it will count as a failure.
*   **start-period**: A quiet period. We’re just giving the app time to start. Failures do not count during this period.

**Build it and run it:**

```
docker build -t docker-http-server .docker run --rm -it --name my-docker-instance -p 8080:8080 docker-http-server
```

You should be able to observe the states changing. There are 3 states:

*   **starting:** In the quiet period
```
CONTAINER ID        STATUS
7b1d13141f78        Up 2 seconds (health: starting)
```
&nbsp;
*   **healthy:** The healthcheck is returning 0
```
CONTAINER ID        STATUS
7b1d13141f78        Up 13 seconds (healthy)
```
&nbsp;
*   **unhealthy:** The healthcheck is returning 1 or the timeout has passed
```
CONTAINER ID        STATUS
7b1d13141f78        Up 36 seconds (unhealthy)
```
&nbsp;
That’s it, I hope you will consider adding a healthcheck next time you build a docker app!