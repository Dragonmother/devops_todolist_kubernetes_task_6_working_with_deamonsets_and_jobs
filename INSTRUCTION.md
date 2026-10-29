# INSTRUCTION

## 1. Deploy the resources

Create the `mateapp` namespace if it does not exist:

```bash
kubectl create namespace mateapp
```

Deploy the DaemonSet and CronJob:

```bash
kubectl apply -f .infrastructure/daemonset.yml
kubectl apply -f .infrastructure/cronjob.yml
```

## 2. Validate the DaemonSet

Check the DaemonSet status and its pods:

```bash
kubectl get daemonset -n mateapp
kubectl get pods -n mateapp -l app=todoapp-checker
```

View DaemonSet logs:

```bash
kubectl logs -n mateapp -l app=todoapp-checker --tail=50
```

The container sends a request to the Todo app's `/api/health` endpoint every five seconds through the `todoapp-service` ClusterIP Service.

## 3. Validate the CronJob

Check the CronJob and its scheduled jobs:

```bash
kubectl get cronjob -n mateapp
kubectl get jobs -n mateapp
```

Wait for a scheduled run or create a test Job immediately:

```bash
kubectl create job --from=cronjob/todoapp-health-check todoapp-health-check-manual -n mateapp
```

Find the Job's pod:

```bash
kubectl get pods -n mateapp
```

View its logs, replacing `<POD-NAME>` with the actual pod name:

```bash
kubectl logs -n mateapp <POD-NAME>
```

The CronJob calls `/api/health` every four minutes. It keeps up to 10 successful Jobs and 5 failed Jobs in history and allows concurrent runs.

## 4. Verify resource configuration

Inspect the resources and configuration:

```bash
kubectl describe daemonset todoapp-checker -n mateapp
kubectl describe cronjob todoapp-health-check -n mateapp
```

Both workloads define CPU and memory requests and limits.
