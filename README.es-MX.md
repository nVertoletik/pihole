# Pihole para k8s
Pihole para k8s tiene como objetivo correr una instancia de pihole en un clúster de Kubernetes, especificamente en uno corriendo sobre raspberrys.

# Instalación
Para ejecutar la instancia de Pihole en tu clúster es necesario crear una storageclass donde se almacenarán a largo plazo los datos.

* Edita el archivo **pihle-pvc.yml** para cambiar la storageclass de las dos persistenvolumeclaims, no olvides guardar los cambios.
* Crea el  namespace **pihole** que alojará todos los recursos de pihole, para crearlo corre el comando `kubectl create -f pihole-pvc.yml` .
* Crea las persistentvolumeclaims con el comando `kubectl create -f pihole-pvc.yml` .
* Edita el archivo **pihole-deployment.yml** para colocar la *WEBPASSWORD* que se utilizará para acceder a la interfáz web y la *TZ* donde te ubicas. No olvides guardar los cambios.
* Crea el deployment ejecutando el comando `kubectl create -f pihole-deployment.yml` .
* Verifica que el pod pihole se encuentra corriendo, ejecutando el comando `kubectl get pod -n pihole`. El resultado debe ser similar al siguiente texto:
```
NAME                      READY   STATUS    RESTARTS   AGE
pihole-7d8f45c9df-r6ftp   1/1     Running   0          1h
```
* Crea el servicio DNS que será utilizado por tus dispositivos para resolver los nombre de dominio ejecutando el comando `kubectl create -f pihole-dns-service.yml`. Si no estás utilizando un proveedor LoadBalancer (por defecto todos los proveedores de servicios en la nube ofrecen este servicio con sus respectivas ofertas como AKS, EKS, GCS, si estás corriendo tu clúster sobre raspberrys puedes instalar metallb para utilizar este tipo de servicio).
* Crea el servicio donde podrás acceder a la interfáz web, donde podrás configurar tu pihole, ejecuta el comando `kubectl create -f pihole-web-service.yml`
* Verifica que ambos servicos están corriendo con el comando `kubectl get svc -n pihole`. La salida debe ser similar a la siguiente:
```
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                   AGE
pihole-dns   LoadBalancer   10.96.202.73   192.168.1.200   53:32036/UDP                              1h
pihole-web   LoadBalancer   10.99.33.92    192.168.1.201   80:30859/TCP,53:31653/TCP,443:30304/TCP   1h
```
* En tu navegador accede a la dirección IP del servicio *pihole-web* del paso anterior,utiliza el valor debajo de **EXTERNAL-IP**. Tomando como ejemplo la salida del paso mencionado accederíamos a la url 192.168.1.201

# Desinstalar
Para eliminar todos los recursos creados, dirigete al directorio donde se encuentran los archivos manifiestos, elimina los recursos ejecutando el comando `kubectl delete -f .`. Para verificar que se han eliminado los recursos de forma correcta ejecuta el comando `kubectl get all -n pihole`. La salida debe ser similar a la siguiente:
```
No resources found in pihole namespace.

```
