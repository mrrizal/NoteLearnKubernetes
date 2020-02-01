menampilkan semua pod yang sedang running: `kubectl get pod`

menampilkan informasi lebih detail mengenai suatu pod: `kubectl describe pod pod_name`

membuka port di pod (create new service)
```
kubectl expose pod pod_name \
--port 444 \
--name frontend
```

port forward dari exposed pod port ke local machine: `kubectl port-forward pod_name 8080`

attach to the pod (gatau ini maksud nya apaan): `kubectl attach pod_name -i`

execute command on the pod: `kubectl exec pod_name -- command`

add label to pod: `kubectl label pods pod_name mylabel=test`

run shell in a pod (very useful for debugging)
```
kubectl run -i \
--tty busybod \
--image busybox \
--restart Never \
-- sh
```
