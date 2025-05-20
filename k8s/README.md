# Apache Kafka (KRaft mode) on Kubernetes

## Deploying a Single-Broker Kafka

1. Create the PersistentVolumeClaim:
   ```sh
   kubectl apply -f kafka-pvc.yaml
   ```
2. Deploy Kafka:
   ```sh
   kubectl apply -f kafka-deployment.yaml
   ```
3. Create the Service:
   ```sh
   kubectl apply -f kafka-service.yaml
   ```

Kafka will be available within the cluster at `kafka:9092` and `kafka:9093`.

---

## Scaling to Multiple Brokers

1. Edit `kafka-deployment.yaml`:
   - Change `replicas: 1` to your desired number (e.g., 3).
   - For each broker, you must:
     - Assign a unique `KAFKA_NODE_ID` (e.g., 1, 2, 3).
     - Update `KAFKA_CONTROLLER_QUORUM_VOTERS` to include all brokers, e.g., `1@kafka-0.kafka-headless:9091,2@kafka-1.kafka-headless:9091,3@kafka-2.kafka-headless:9091`.
     - Use a StatefulSet instead of a Deployment for stable network IDs and storage.

2. Update the manifests accordingly and re-apply.

---

## Exposing Kafka Externally

- To access Kafka from outside the cluster, change the Service type in `kafka-service.yaml` from `ClusterIP` to `NodePort` or `LoadBalancer`.
- Example:
  ```yaml
  spec:
    type: NodePort
    # ...
  ```
- Then, connect to the node's IP and the assigned port.

---

## Notes
- This setup uses the `apache/kafka-native` image in KRaft mode (no Zookeeper).
- For production, use a StatefulSet and configure persistent storage for each broker. 