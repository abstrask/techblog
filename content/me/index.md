---
title: About me
---

{{< image src="/images/portrait.jpg" alt="Portrait photo" linked="false" position="center" >}}

<!--more-->

## Resumé

As a seasoned Senior Cloud Engineer, I architect and deploy robust, scalable cloud-native solutions using AWS, Kubernetes, and Terraform. My goal is to lower the barrier of entry for teams to leverage cloud and container technologies, helping bring their ideas to life.

The foundation for this, I achieve through an AWS "account factory" with preconfigured DNS domain delegation, OIDC providers, IAM and global backup policies. This makes it easy for teams to get a place to run their apps, while reducing blast radius to each account.

I'm one of the main architects of our internal EKS Kubernetes-based platform "with batteries included", equipped with features such as support for AI/GPU workloads, auto-scaling (including to/from zero), long-running batch jobs, declarative blue/green node groups, spot instances, and more. We also manage the full life-cycle of these clusters, keeping up all components up-to-date with a minimum of manual intervention.

As a DevOps advocate, I offer internal consultancy on AWS cloud best practices, guiding teams to architect secure, scalable, and cost-effective solutions.

I consider myself an expert in Terraform, rigorously defining infrastructure-as-code, and automating processes using tools like GitHub Actions, Python, and FaaS. Grafana - both self-hosted and Grafana Cloud - has been my observability tool of choice since the beginning, so I'm well-versed in PromQL and CloudWatch Metrics queries, as well as configuring and managing Grafana itself.

TL;DR: I have been living and breathing AWS, Kubernetes ([since 1.9](https://kubernetes.io/blog/2017/12/kubernetes-19-workloads-expanded-ecosystem/), AWS EKS [since launch day](https://aws.amazon.com/blogs/aws/amazon-eks-now-generally-available/)), Terraform ([since 0.11](https://www.hashicorp.com/en/blog/hashicorp-terraform-0-11)), Grafana and automation since 2018, on top of 18 years as a SysAdmin.

## Main skills

- Kubernetes and Amazon EKS
- Amazon Web Services
- Terraform
- Platform Engineering
- Grafana (Cloud)

## Other achievements

- [**Flux Helm Diff**](../posts/flux-helm-diff) creator: A GitHub Action that renders [Flux CD](https://fluxcd.io/) Helm manifests on pull requests, 100% client-side, and posts the diff as a [comment on the PR](https://github.com/marketplace/actions/flux-helm-diff#example-outputpr-comment)
- **Kubernetes Cluster Autoscaler** contributor: Identified and documented a [bug](https://github.com/kubernetes/autoscaler/issues/6481), together with my colleague, {{< person url="https://wcarlsen.github.io/" name="Willi Carlsen" picture="https://avatars.githubusercontent.com/u/17003032" >}}. Then worked with him to [develop af fix](https://github.com/kubernetes/autoscaler/pull/6482), which was included in release [1.31.0](https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.31.0).
- Devised an **AWS EKS "blue/green" node group abstraction**, allowing us to apply configuration updates, without disrupting long-running batch jobs (with {{< person url="https://wcarlsen.github.io/" name="Willi Carlsen" picture="https://avatars.githubusercontent.com/u/17003032" >}})
- **Certified Kubernetes Administrator (CKA)** in 2020

## Recent Experience

### Veo Technologies ApS

**Senior Cloud Engineer** (May 2022 - Present)

Each week, we process and analyse upwards of 60,000 videos at Veo, with massive fluctuation throughout the days and hours, going back and forth between near-0 and full speed at around 2,500 nodes.

Me and my team design, build and operate the Kubernetes-based platform for teams, that ensures our customers get their videos and analytics swiftly.

As we grow, we meet and overcome new limitations all the time, while continuing to improve efficiency and lower the error rate.

### DFDS A/S

**Cloud Chapter Lead** (July 2020 - April 2022)

**Site Reliability Engineer** (January 2018 - April 2022)

**IT Administrator** (August 2011 - Januar 2018)

Part of the Cloud Engineering team in DFDS. We strive to pave the road, letting all DFDS' developers deliver their software on Kubernetes and AWS faster and easier.

Designing, building, operating Kubernetes clusters. Automation and Infrastructure-as-code, primarily through Terraform, but also PowerShell, Bash scripts and pipelines.

Spend most of my time these days in Amazon Web Services, but also experience with Azure (Azure AD/Entra, access management, hybrid networking, web apps, StorSimple hybrid file-server etc.).

### Earlier

- 18 years of general Windows client, server and cluster administration, going back to NT 4.0
- Architecting, deploying and managing Citrix XenApp remote applications for 24x7 operations
- Architecting, deploying and managing distributed, multi-site Active Directory and Exchange email systems
- Architecting, deploying and managing VMWare and Hyper-V clusters at multiple locations
- Deploying and managing various Storage Area Networks (SANs) from Dell/EMC, HP and 3Par

Full details on my [LinkedIn profile](https://www.linkedin.com/in/rasmusrask/details/experience/).