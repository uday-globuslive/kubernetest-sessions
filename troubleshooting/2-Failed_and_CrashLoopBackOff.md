Failed State:

1. A pod enters the Failed state when all containers within the pod have terminated, and at least one container has terminated in failure (i.e., exited with a non-zero status code).

2. This state is typically final, meaning Kubernetes won't automatically try to restart the pod.

3. Common scenarios leading to Failed state:
   - The main application process exits with an error code.
   - An init container fails to complete successfully.

4. Example:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: fail-pod
   spec:
     containers:
     - name: fail-container
       image: busybox
       command: ["/bin/sh", "-c", "exit 1"]
   ```
   This pod will immediately enter Failed state because the container exits with status code 1.

CrashLoopBackOff State:

1. A pod enters CrashLoopBackOff when one of its containers exits repeatedly and Kubernetes has tried to restart it several times.

2. This is not a final state. Kubernetes will continue to try restarting the container, but with an exponentially increasing delay between attempts (10s, 20s, 40s, etc., up to 5 minutes).

3. Common scenarios leading to CrashLoopBackOff:
   - Application crashes due to bugs or misconfigurations.
   - Container can't start due to missing dependencies or incorrect startup commands.
   - Resource constraints preventing the container from starting properly.

4. Example:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: crashloop-pod
   spec:
     containers:
     - name: crashloop-container
       image: busybox
       command: ["/bin/sh", "-c", "sleep 10 && exit 1"]
   ```
   This pod will enter CrashLoopBackOff because it keeps exiting after 10 seconds, and Kubernetes keeps trying to restart it.

Key Differences:

1. Restart Behavior:
   - Failed: Kubernetes doesn't automatically restart the pod.
   - CrashLoopBackOff: Kubernetes repeatedly attempts to restart the pod.

2. Duration:
   - Failed: It's an immediate, final state after all containers terminate.
   - CrashLoopBackOff: It's an ongoing state with repeated crash and restart cycles.

3. Troubleshooting Approach:
   - Failed: Check the pod's logs and describe output for the final error that caused termination.
   - CrashLoopBackOff: Investigate ongoing issues, possibly related to application startup or configuration.

To observe these states:

1. For Failed state:
   ```
   kubectl apply -f fail-pod.yaml
   kubectl get pods
   kubectl describe pod fail-pod
   ```

2. For CrashLoopBackOff state:
   ```
   kubectl apply -f crashloop-pod.yaml
   kubectl get pods
   kubectl describe pod crashloop-pod
   ```

In both cases, use `kubectl logs <pod-name>` to check the container logs for error messages.

Understanding these states is crucial for effective troubleshooting in Kubernetes. Failed state often indicates a clear, one-time error, while CrashLoopBackOff suggests an ongoing issue that needs investigation into the pod's configuration or the application's behavior.
