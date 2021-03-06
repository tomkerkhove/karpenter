# Karpenter
![](./docs/images/logo.jpeg)

Karpenter is a metrics-driven autoscaler for Kubernetes. It's performant, extensible, and can autoscale anything that implements the Kubernetes [scale subresource](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/autoscaling/horizontal-pod-autoscaler.md#scale-subresource).

Karpenter is built for Kubernetes and can run in any Kubernetes cluster anywhere.

This is an early stage, experimental project built with ❤️ and is available as a **developer preview**. We're excited you are here - jump in, let us know what you think. We welcome contributions.

## Getting Started
We will learn about Karpenter's APIs, look at some sample configurations, and install Karpenter's Controller. Alternatively, you can dive right into the [demo](https://github.com/ellistarn/karpenter-aws-demo).

### APIs
Karpenter defines three custom resources to configure autoscaling behavior.

**[HorizontalAutoscalers](./pkg/apis/autoscaling/v1alpha1/horizontalautoscaler.go)** define your autoscaling policy. It's modeled closely after the [HorizontalPodAutoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/), but has been generalized to support autoscaling for arbitrary resources. HorizontalAutoscalers periodically query metrics configured by `spec.metrics`, compute an autoscaling decision controlled by `spec.behavior`, and adjust the replicas of their `spec.scaleTargetRef`. Unlike the HPA, Karpenter's HorizontalAutoscalers integrate directly with Prometheus and can use any [promql](https://prometheus.io/docs/prometheus/latest/querying/basics/) response of type "instant vector" in their calculations.

**[MetricsProducers](./pkg/apis/autoscaling/v1alpha1/metricsproducer.go)** generate Prometheus metrics for commonly used autoscaling use cases. They periodically calculate a metric based on their configuration and expose it at a metrics endpoint that can be scraped by Prometheus. If you already have metrics you wish to use for autoscaling available in Prometheus, it is not necessary to define a Metrics Producer.

**[ScalableNodeGroups](./pkg/apis/autoscaling/v1alpha1/scalablenodegroup.go)** provide a minimal way to point a HorizontalAutoscaler's `scaleTargetRef` to a Cloud Provider's Node Group API. Kubernetes core does not define an abstraction for Node Group. Instead, Cloud Providers typically expose non-Kubernetes Node Group APIs. ScalableNodeGroups are a shim in front of these APIs that are limited to `spec.replicas` and `status.replicas`. It is not a replacement or wrapper for these APIs. If you're using a solution that provides a Kubernetes API (e.g. [Kops](https://github.com/kubernetes/kops) or [Cluster API](https://github.com/kubernetes-sigs/cluster-api)), you can point the HorizontalAutoscaler's `scaleTargetRef` to these resources instead of a ScalableNodeGroup.

[Learn more](./docs) about the different ways to configure Karpenter's resources.

## Installation
Follow the setup recommendations of your cloud provider.
- [AWS](./docs/aws/README.md#installation)

Then install the controller.
```
CLOUD_PROVIDER=aws
VERSION=v0.1.0
kubectl apply -f https://raw.githubusercontent.com/awslabs/karpenter/master/releases/${CLOUD_PROVIDER}/${VERSION}.yaml
```

## Docs
- [Examples](./docs/examples)
- [Working Group](./docs/working-group)
- [Developer Guide](./docs/DEVELOPER_GUIDE.md)
- [Design](./docs/DESIGN.md)
- [FAQs](./docs/FAQs.md)
- [Contributing](./docs/CONTRIBUTING.md)

## Terms
Karpenter is an early stage, experimental project that is currently maintained by AWS and available as a preview. We request that you do not use Karpenter for production workloads at this time. See details in our [terms](./docs/TERMS.md).

## License
This project is licensed under the Apache-2.0 License.
