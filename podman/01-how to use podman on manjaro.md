Here's a step-by-step guide on how to use Podman on Manjaro:

1. Install Podman:
   Open a terminal and run:
   ```
   sudo pacman -S podman
   ```

2. Start the Podman service:
   ```
   sudo systemctl start podman.socket
   ```

3. Enable the Podman service to start on boot:
   ```
   sudo systemctl enable podman.socket
   ```

4. Verify the installation:
   ```
   podman --version
   ```

5. Pull an image:
   ```
   podman pull docker.io/library/ubuntu:latest
   ```

6. List downloaded images:
   ```
   podman images
   ```

7. Run a container:
   ```
   podman run -it --name mycontainer ubuntu:latest /bin/bash
   ```

8. Exit the container:
   Type `exit` or press Ctrl+D

9. List running containers:
   ```
   podman ps
   ```

10. List all containers (including stopped ones):
    ```
    podman ps -a
    ```

11. Start a stopped container:
    ```
    podman start mycontainer
    ```

12. Stop a running container:
    ```
    podman stop mycontainer
    ```

13. Remove a container:
    ```
    podman rm mycontainer
    ```

14. Remove an image:
    ```
    podman rmi ubuntu:latest
    ```

15. Create a Podman pod:
    ```
    podman pod create --name mypod
    ```

16. Run a container in a pod:
    ```
    podman run -dt --pod mypod --name container1 ubuntu:latest
    ```

17. Inspect a pod:
    ```
    podman pod inspect mypod
    ```

18. Remove a pod (and its containers):
    ```
    podman pod rm -f mypod
    ```

19. Build an image from a Dockerfile:
    ```
    podman build -t <image-name> <path-to-dockerfile>
    ```

20. Push an image to a registry:
    ```
    podman push <image-name> <registry-url>
    ```

21. To use Podman without root privileges, add your user to the `podman` group:
    ```
    sudo usermod -aG podman $USER
    ```
    Log out and log back in for the changes to take effect.
