## KEDA with RabbitMQ StatefulSet

This project demonstrates how to utilize KEDA (Kubernetes Event-Driven Autoscaler) in conjunction with a RabbitMQ StatefulSet to dynamically scale pods based on message backlog within RabbitMQ queues. By leveraging KEDA's ScaledObject custom resource, the system automatically adjusts the number of RabbitMQ pods to handle fluctuating message volumes.

## Functionality

- KEDA monitors the message backlog of RabbitMQ queues.
- Once the backlog exceeds a predefined threshold, KEDA triggers a Horizontal Pod Autoscaler (HPA) to scale the RabbitMQ StatefulSet up or down.
- This ensures that RabbitMQ can efficiently process incoming messages without bottlenecks or resource constraints.

## Benefits

- **Elastic Scaling:** Automatic scaling based on real-time message volume optimizes resource utilization and cost-efficiency.
- **Improved Performance:** Prevents message backlogs and ensures timely message processing, leading to a more responsive and performant RabbitMQ deployment.
- **Simplified Management:** KEDA automates scaling decisions, reducing manual intervention and configuration complexity.

## Prerequisites

- Kubernetes cluster with KEDA installed.
- RabbitMQ deployment (StatefulSet recommended for persistent message storage).

## Deployment Instructions

1. **Configure RabbitMQ:**
   - Set up your RabbitMQ deployment, including the desired number of initial pods and relevant resource specifications.
   - Consider using a StatefulSet to ensure persistent message storage across restarts.

2. **Define KEDA ScaledObject:**
   - Create a `ScaledObject` manifest file, specifying:
     - The name of the RabbitMQ deployment/StatefulSet.
     - The RabbitMQ queue(s) to monitor.
     - The scaling metric (e.g., `rabbitmq-queues-messages`).
     - The threshold value that triggers scaling (e.g., desired message backlog level).
     - The HPA configuration (min/max replicas, CPU/memory limits, etc.).
   - You can use tools like `keda cli` or the Kubernetes API to create the `ScaledObject` manifest.

## Example ScaledObject Manifest

```yaml
apiVersion: keda.k8s.io/v1alpha1
kind: ScaledObject
metadata:
  name: rabbitmq-scaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment  # Replace with "StatefulSet" if you're using one
    name: rabbitmq-deployment
  triggers:
  - type: rabbitmq-queues
    metadata:
      name: my-queue
      source: my-rabbitmq-service  # Replace with your RabbitMQ service name
  cooldown: 60s  # Optional cooldown period after scaling actions
```

## Verification

- Deploy the KEDA ScaledObject resource.
- Send test messages to your RabbitMQ queue(s).
- Observe the RabbitMQ pod count as it scales up or down based on the message backlog and the defined thresholds.


