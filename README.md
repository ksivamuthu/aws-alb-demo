# AWS Load Balancer Controller Demo

1. Create a cluster using EKSCTL

```
eksctl create cluster --config-file eks-cluster.yaml
```

2. Install the AWS Load Balancer Controller using Helm chart

```
helm repo add eks https://aws.github.io/eks-charts
kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master"
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=*<cluster-name>* --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer
```

3. Install the echoserver yaml. It has the annotation, that will create a NLB for this service.

```
kubectl apply -f deploy/echoserver.yaml
```

The annotations are

```
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: external # Note this external is not "internal/external", 
    # it's denoting the responsibility of creating load balancer is external of built-in kubernetes services
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: instance
    service.beta.kubernetes.io/aws-load-balancer-scheme: internal 
```

Please note, the aws load balancer type is external. The type has to be either nlb-ip or external.  Note this external is not "internal/external", it's denoting the responsibility of creating load balancer is external of built-in kubernetes services

4. You can add the custom certificate ARN and the external dns records. This [link](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.1/guide/integrations/external_dns/) will help you.

Let me know if you've any questions. I will walk you through.

The blog I've written on AWS ALB controller is here - https://dev.to/aws-builders/aws-load-balancer-controller-on-eks-cluster-38fk
