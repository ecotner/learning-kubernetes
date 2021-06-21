# Creating Pods

## Basics
You can create a pod from a docker image by simply using the `k run ...` command.
I am going to use the base `nginx:alpine` image for these examples.
Once you have the image, you can deploy a pod by simply running:
```bash
k run my-nginx --image=nginx:alpine
```
Check that you have the pod running by
```bash
k get pods
```
and you should see some info about the status of the nginx pod.
Once the pod is up and running, you can run a port forward from 80 (the internal port) to some arbitrary external port 8080:
```bash
k port-forward my-nginx 8080:80
```
You can make a request of the webserver by going to [http://localhost:8080](http://localhost:8080) and see the default splash page.

One cool test to show that kubernetes is actually doing its job is you can kill the container and watch k8s bring it back up.
Check out the running containers with `docker ps` and you'll see one that starts with `k8s_my-nginx...`.
Run `docker kill <container ID>`, wait a bit and run `docker ps` again and you'll see it's still there, but the status says that the container's only been up a couple seconds.
Run `k get pods` and you'll see that the counter under RESTARTS has incremented by one, which means that the container died, but the pod remade it and brought it back!
To delete the pod, run
```bash
k delete pod my-nginx
```

## Launching with YAML
One convenient way of managing pods is by using a YAML file to declare how you want it to be set up.
I have a [sample YAML file here](./nginx.pod.yaml) for setting up our nginx server you can check out.
At the bare minimum, we're going to need to specify the API version (v1), resource type (Pod), name (my-nginx), and the container spec (container name and image, also port if necessary).
We can launch a pod using the configuration in this file by
```bash
k create -f nginx.pod.yaml
```
`k create ...` will fail if the pod is already running, but you can use `k apply ...` to _modify_ a pod based on an updated configuration and k8s will automatically adjust.
You can get a whole bunch on information about the running pod by using
```bash
k describe pod my-nginx
```
including a history of all events at the bottom.
(Try killing the container again and see it get brought back in the history; it will say "x2" in the "Age" description of some events.)
You can execute a command directly in a pod just like `docker exec` by using `k exec`; for example:
```bash
k exec -it my-nginx sh
```
to open an interactive shell.
You can directly edit the configuration of the pod using
```bash
k edit -f nginx.pod.yaml
```
And finally, you can delete the pod using
```bash
k delete -f nginx.pod.yaml
```

## Pod health
Kubernetes monitors the health of your pods using "probes".
There are two types of probes: "liveness" and "readiness".
Liveness checks the overall health of the pod and determines if it needs to be torn down/restarted, while readiness checks whether the pod is ready to start receiving requests/traffic.
There are several methods of implementing probes, such as performing an action in the container, making a TCP request, or an HTTP request.
[Check out the YAML file](./nginx.pod.yaml) for an example of a `httpGet` request implementing a `livenessProbe` and `readinessProbe`, and [this file](./liveness.pod.yaml) for an example of a `exec` probe that checks for the presence of a file which is deleted shortly after the container starts.
