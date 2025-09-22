---
title: Securely passing AWS IAM credentials to pods running in any Kubernetes cluster
date: 2022-02-06
excerpt: | 
    Passing aws credentials securely to apps running in a Kubernetes cluster is trivial thing. we use KIAM before
    to pass credentials to pod. But its depricated as aws has now a very.
authors:
- Hemant Negi
---

Securely passing AWS IAM credentials to pods running in any Kubernetes cluster

to create an https://docs.aws.amazon.com/eks/latest/userguide/connector_IAM_role.html

- First we need to create an IAM role for your cluster
- Register a cluster in EKS with custom name and select the role created above.
- It ask you to download a yaml file (It will ask you only once so be sure to 
  download that yaml file without reloading the page.)

- Apply the yaml an it will bring up a pod

Next is we need to create a
curl -o eks-connector-clusterrole.yaml https://amazon-eks.s3.us-west-2.amazonaws.com/eks-connector/manifests/eks-connector-console-roles/eks-connector-clusterrole.yaml

- https://docs.aws.amazon.com/eks/latest/userguide/connector-grant-access.html

The thing is, we need an open ID Connect issuer isntalled/connected with our
cluster. That will take time.


Flags required to enable oidc in k8s cluster

- --oidc-issuer-url=<k8s_oidc_issuer_url> # <-- 🔴 update this
    - --oidc-client-id=<k8s_oidc_client_id> # <-- 🔴 update this
    - --oidc-username-claim=email # <-- 🔴 update this
    - --oidc-groups-claim=groups # <-- 🔴 update this

This article is great.
https://mjarosie.github.io/dev/2021/09/15/iam-roles-for-kubernetes-service-accounts-deep-dive.html
https://itnext.io/protect-kubernetes-dashboard-with-openid-connect-104b9e75e39c

## Steps in aws IAM identity providers
For Provider Type, choose OpenID Connect.
For Provider URL, paste the OIDC issuer URL for your cluster, and then choose Get thumbprint.
For Audience, enter sts.amazonaws.com and choose Add provider.

 
## Creating an OIDC
here is a go library that you can drop in your go project to add OIDC provider support.
```
Service linked role 'arn:aws:iam::751498470000:role/aws-service-role/eks-connector.amazonaws.com/AWSServiceRoleForAmazonEKSConnector' is propagating, please retry later.
```


Troubleshoting - https://docs.aws.amazon.com/eks/latest/userguide/troubleshooting-connector.html