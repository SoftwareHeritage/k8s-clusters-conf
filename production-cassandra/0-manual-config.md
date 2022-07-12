# patch of the cluster configuration in rancher

## Prometheus local volumes on rancher-node-cassandra[1-3]
```
--- /tmp/cluster-orig.yaml	2022-07-12 11:27:27.169509573 +0200
+++ /tmp/cluster.yaml	2022-07-12 11:26:54.865395186 +0200
@@ -58,6 +58,8 @@
       service_node_port_range: 30000-32767
     kube-controller: {}
     kubelet:
+      extra_binds:
+        - '/srv/prometheus:/srv/prometheus'
       fail_swap_on: false
       generate_serving_certificate: false
     kubeproxy: {}
```

# Labels

```
kubectl label node rancher-node-cassandra1 prometheus=true
kubectl label node rancher-node-cassandra2 prometheus=true
kubectl label node rancher-node-cassandra3 prometheus=true
```

# Prometheus operator
In rancher application

```
--- /tmp/prometheus-orig.yaml	2022-07-12 11:54:55.115414904 +0200
+++ /tmp/prometheus.yaml	2022-07-12 11:48:22.501966971 +0200
@@ -899,7 +899,7 @@
     remoteWriteDashboards: false
     replicaExternalLabelName: ''
     replicaExternalLabelNameClear: false
-    replicas: 1
+    replicas: 3
     resources:
       limits:
         cpu: 1000m
@@ -930,6 +930,14 @@
         spec:
           accessModes:
             - ReadWriteOnce
+          resources:
+            requests:
+              storage: 30Gi
+          selector:
+            matchLabels:
+              prometheus: 'true'
+          storageClassName: local-storage
+          volumeMode: Filesystem
     thanos: {}
     tolerations: []
     topologySpreadConstraints: []
```
