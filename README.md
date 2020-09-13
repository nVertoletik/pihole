# Pihole for k8s
Pihole for k8s aims to run a pihole instance in a Kubernetes cluster, specially one running on raspberry pies.

# Install
To run pihole in your cluster, we need to provision a storage class where long term data will be stored.

* Edit the **pihle-pvc.yml** file to change the StorageClass of the 2 PersistenVolumeClaims and save the changes
* Create the **pihole** namespace where all the resources will be created running `kubectl create -f pihole-pvc.yml`
* Create the PersistentVolumeClaims with `kubectl create -f pihole-pvc.yml`
* Edit the **pihole-deployment.yml** file to set the *WEBPASSWORD* used to access to the web interface and the *TZ* where you are located. Don't forget to make the changes
* Create the deployment running `kubectl create -f pihole-deployment.yml`
* Verify that the pihole pod is running with `kubectl get pod -n pihole`. The result should be similar to the following output:
```
NAME                      READY   STATUS    RESTARTS   AGE
pihole-7d8f45c9df-r6ftp   1/1     Running   0          1h
```
* Create the DNS service that will be used by your devices to resolve domain names executing `kubectl create -f pihole-dns-service.yml`. If you are not using a LoadBalancer provider (by default all cloud providers offer this services with their respective offerings, if you are running your cluster in a raspberry pi cluster you can install metallb to use this kind of service).
* Create the web interface service where you will be managing your pihole setup, run `kubectl create -f pihole-web-service.yml`
* Verify that both services are running with `kubectl get svc -n pihole`. The output should be similar to
```
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                   AGE
pihole-dns   LoadBalancer   10.96.202.73   192.168.1.200   53:32036/UDP                              1h
pihole-web   LoadBalancer   10.99.33.92    192.168.1.201   80:30859/TCP,53:31653/TCP,443:30304/TCP   1h
```
* In your browser access to the IP address of the *pihole-web* service found in the previous step, use the value under **EXTERNAL-IP**

# Uninstall
To remove the resources created go to the directory where the manifests are found and just delete them with `kubectl delete -f .`. To ensure that the resources were deleted run `kubectl get all -n pihole`. The output should be similar to
```
No resources found in pihole namespace.

```
