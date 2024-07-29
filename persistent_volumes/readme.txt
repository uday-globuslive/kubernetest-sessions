FROM busybox
RUN mkdir /myvolume
RUN echo "hello world" > /myvolume/hello.txt
VOLUME /myvolume


docker container run -dt --name demo -v myvolume:/myvolume image:tag sh

ls /var/lib/docker/volumes/myvolume/_data


-----

## direct volume created in pod spec for hostfile type.

apiVersion: v1
kind: Pod
metadata:
  name: demopod-volume
spec:
  containers:
  - image: nginx
    name: test-container
    volumeMounts:
    - mountPath: /data
      name: first-volume
  volumes:
  - name: first-volume
    hostPath:
      path: /mydata
      type: Directory

kubectl exec -it demopod-volume bash
root# cd /data

--
