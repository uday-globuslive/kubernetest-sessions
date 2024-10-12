 Here are step-by-step instructions on how to use Podman on Ubuntu:

1. Install Podman:
   ```
   sudo apt update
   sudo apt install -y podman
   ```

2. Verify the installation:
   ```
   podman --version
   ```

3. Pull an image:
   ```
   podman pull ubuntu:latest
   ```

4. List available images:
   ```
   podman images
   ```

5. Run a container:
   ```
   podman run -it --name my_container ubuntu:latest /bin/bash
   ```

6. Exit the container:
   ```
   exit
   ```

7. List running containers:
   ```
   podman ps
   ```

8. List all containers (including stopped ones):
   ```
   podman ps -a
   ```

9. Start a stopped container:
   ```
   podman start my_container
   ```

10. Stop a running container:
    ```
    podman stop my_container
    ```

11. Remove a container:
    ```
    podman rm my_container
    ```

12. Remove an image:
    ```
    podman rmi ubuntu:latest
    ```

13. Build an image from a Dockerfile:
    ```
    podman build -t my_image:tag .
    ```

14. Push an image to a registry:
    ```
    podman push my_image:tag registry.example.com/my_image:tag
    ```

15. Create a pod:
    ```
    podman pod create --name my_pod
    ```

16. Run a container in a pod:
    ```
    podman run --pod my_pod -d nginx
    ```

17. Inspect a container:
    ```
    podman inspect my_container
    ```

18. View container logs:
    ```
    podman logs my_container
    ```

19. Execute a command in a running container:
    ```
    podman exec -it my_container /bin/bash
    ```

20. Clean up unused resources:
    ```
    podman system prune
    ```
