---
layout: post
title: Hosting containerised apps on EC2 using an ALB
subtitle: >-
  Sample architecture (Path based routing & SSO).
date: '2021-08-06T15:20:31.531Z'
thumbnail-img: /assets/img/path_based_routing.png
categories: []
keywords: []
tags: [ec2, devops, aws, alb, ci/cd, docker, containers]
readtime: true
---

## K8S has not saved the entire the world (yet)

I bet that the first word that comes to your mind when you hear the words microservices and containers is K8S. Well, guess what! Not everyone is using Kubernetes and it is absolutely fine! As with every piece of technology K8S aims to solve specific problems. However, if you are not facing these problems but you also want to take advantage of the containerisation benefits, then you can host your containers on traditional compute instances/resources like EC2 or Fargate. In this blogpost I am going to focus on a problem (oops, I meant to say challenge) I recently faced when I had to deploy multiple containers on the same ec2 instance. The reason for doing this is of course... cost. However, I also wanted to be able to scale the infrastructure as needed and take advantage of the containerisation benefits. Let me describe the challenge then!

### Problem

- I had to run a microservices-based application consisting of many different containers which had to be part of specific target groups.

- Each microservice/container was exposing a different endpoint. The endpoint had to be accessible from the same url and port - SSL(443). To make it more clear, my endpoints map  looked like below:

```
Microservice 1 : <app_endpoint>/service/one/...
Microservice 2 : <app_endpoint>/service/two/...
Microservice 3 : <app_endpoint>/service/three/...
Microservice 4 : <app_endpoint>/service/four/...
```

- Additionally, the endpoint of Microservice 4 had to be protected and users shouldn't be able to access it without authentication. That meant I had to enable SSO for this endpoint.

### Sample Solution

There are multiple ways to solve this problem but I'd like to present a solution based on the usage of an ALB and its features. The ALB is extremely powerful and feature rich and it is allowing us to build really nice flows with our containers. Specifically, the ALB allows us to execute path based routing but also has built-in authentication support. Back to our problem, I was mostly concerned about my URLs/endpoints and how these were exposed.

Creating target groups according to the URL each microservice is exposing (e.g. a target group running only instances of microservice type 1) is not a good solution, mostly because it is a waste of resources and it is limiting our ability to place containers wherever it is cheaper (using spot instances, autoscaling groups etc.). Other than that, creating multiple listeners that listen on different ports and redirecting traffic to the corresponding target group, is also not a good option. I did not want to puzzle the end users with providing many different ports for the same application/service. (The fact that the service is containerised does not mean that we also have to break down its entry-point to many other entry-points/ports.)

To sum up, I 've just wanted to share one application endpoint while also being able to scale the application. This makes it easier to maintain the service from an infrastructure point of view while keeping the cost low. Let's go have a look in the sample solution.

**Path based routing**

An elegant solution for this problem is to use path based routing. This allows us to be flexible and also scale the application as needed by adding more ec2 instances in the target group or more target groups (e.g. for an additional service). This solution helps us to keep the cost low while maintaining a level of flexibility in terms of what is deployed where.

<p align="center">
  <img src="/assets/img/path_based_routing.png">
</p>


In terraform we will need a listener rule that would look like below:

```
resource "aws_lb_listener_rule" "static" {
  listener_arn = aws_lb_listener.front_end.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.static.arn
  }

  condition {
    path_pattern {
      values = ["/service/one*"]
    }
  }
}
```

**SSO Authentication with OIDC**

As I mentioned earlier, the endpoint for Microservice 4 (<app_endpoint>/service/four/... ) had to be protected. Again, the ALB provides a really
easy and automation-friendly solution. In this case I 've used OIDC, an authorisation layer built on OAuth 2.0 protocol. Another solution would be to use Amazon's Cognito service. Again here's how the terraform code would look lke in order to authenticate our users and then forward them to the specified endpoint.

```
resource "aws_lb_listener_rule" "oidc" {
  listener_arn = aws_lb_listener.front_end.arn

  action {
    type = "authenticate-oidc"

    authenticate_oidc {
      authorization_endpoint = "https://example.com/authorization_endpoint"
      client_id              = "client_id"
      client_secret          = "client_secret"
      issuer                 = "https://example.com"
      token_endpoint         = "https://example.com/token_endpoint"
      user_info_endpoint     = "https://example.com/user_info_endpoint"
    }
  }

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.static.arn
  }
}
```


**Links:**

<https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb_listener_rule>

<https://aws.amazon.com/blogs/containers/using-aws-application-load-balancer-path-based-routing-to-combine-amazon-ecs-launch-types/>
