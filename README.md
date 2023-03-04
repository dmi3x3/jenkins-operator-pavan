### Установка jenkins-operator



[Начал отсюда](https://jenkinsci.github.io/kubernetes-operator/docs/getting-started/latest/installing-the-operator/#Configuration)

пока без namespaces

```shell
helm repo add jenkins https://raw.githubusercontent.com/jenkinsci/kubernetes-operator/master/chart
```


[Далее посмотрел здесь](https://medium.com/swlh/introduction-to-jenkins-operator-f4cb7ebc2e0b)

```shell
helm install jenkins-operator jenkins/jenkins-operator --set jenkins.enabled=false
```

ПОдготавливаю кубер:

```shell
kubectl apply -f jenkins-operator-user-configuration.yaml
kubectl apply -f jenkins-conf-secrets.yaml
```

Устанавливаю инстанс:

```shell
kubectl apply -f jenkins-instance-pavan.yaml
```

Под появился, но в статусе Error


```shell
dmitriy@pegasix:~/jenkins-pavan$ kubectl get all --all-namespaces 
NAMESPACE     NAME                                    READY   STATUS    RESTARTS      AGE
default       pod/jenkins-operator-5f559cf674-b5fdv   1/1     Running   0             13h
default       pod/jenkins-prod-jenkins                0/1     Error     0             5s
kube-system   pod/coredns-565d847f94-kqh7t            1/1     Running   0             13h
kube-system   pod/etcd-minikube                       1/1     Running   0             13h
kube-system   pod/kube-apiserver-minikube             1/1     Running   0             13h
kube-system   pod/kube-controller-manager-minikube    1/1     Running   0             13h
kube-system   pod/kube-proxy-tbfb2                    1/1     Running   0             13h
kube-system   pod/kube-scheduler-minikube             1/1     Running   0             13h
kube-system   pod/storage-provisioner                 1/1     Running   1 (13h ago)   13h

NAMESPACE     NAME                                          TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                  AGE
default       service/jenkins-operator-http-prod-jenkins    LoadBalancer   10.100.12.36   <pending>     8080:30019/TCP           144m
default       service/jenkins-operator-slave-prod-jenkins   ClusterIP      10.96.96.83    <none>        50000/TCP                144m
default       service/kubernetes                            ClusterIP      10.96.0.1      <none>        443/TCP                  13h
kube-system   service/kube-dns                              ClusterIP      10.96.0.10     <none>        53/UDP,53/TCP,9153/TCP   13h

NAMESPACE     NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   13h

NAMESPACE     NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
default       deployment.apps/jenkins-operator   1/1     1            1           13h
kube-system   deployment.apps/coredns            1/1     1            1           13h

NAMESPACE     NAME                                          DESIRED   CURRENT   READY   AGE
default       replicaset.apps/jenkins-operator-5f559cf674   1         1         1       13h
kube-system   replicaset.apps/coredns-565d847f94            1         1         1       13h
```
в логах пода нет сообщения о причине ошибки:

```shell
dmitriy@pegasix:~/jenkins-pavan$ kubectl logs jenkins-prod-jenkins jenkins-master
Error from server (BadRequest): container "jenkins-master" in pod "jenkins-prod-jenkins" is waiting to start: ContainerCreating
dmitriy@pegasix:~/jenkins-pavan$ kubectl logs jenkins-prod-jenkins jenkins-master
Printing debug messages - begin  
+ '[' true == true ']'
+ echo 'Printing debug messages - begin'
+ id
uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins)
+ env
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_PORT=tcp://10.96.96.83:50000
KUBERNETES_SERVICE_PORT_HTTPS=443
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_PORT_50000_TCP=tcp://10.96.96.83:50000
KUBERNETES_SERVICE_PORT=443
HOSTNAME=jenkins-prod-jenkins
JENKINS_UC_EXPERIMENTAL=https://updates.jenkins.io/experimental
JAVA_HOME=/opt/java/openjdk
JENKINS_OPERATOR_HTTP_PROD_JENKINS_PORT_8080_TCP_PROTO=tcp
JENKINS_INCREMENTALS_REPO_MIRROR=https://repo.jenkins-ci.org/incrementals
JENKINS_OPERATOR_HTTP_PROD_JENKINS_PORT_8080_TCP=tcp://10.100.12.36:8080
JAVA_OPTS=-XX:MinRAMPercentage=50.0 -XX:MaxRAMPercentage=80.0 -Djenkins.install.runSetupWizard=false -Djava.awt.headless=true
JENKINS_OPERATOR_HTTP_PROD_JENKINS_SERVICE_PORT=8080
COPY_REFERENCE_FILE_LOG=/var/lib/jenkins/copy_reference_file.log
PWD=/
JENKINS_SLAVE_AGENT_PORT=50000
JENKINS_VERSION=2.375.3
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_PORT_50000_TCP_PROTO=tcp
HOME=/var/jenkins_home
LANG=C.UTF-8
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
JENKINS_OPERATOR_HTTP_PROD_JENKINS_PORT_8080_TCP_PORT=8080
JENKINS_OPERATOR_HTTP_PROD_JENKINS_SERVICE_HOST=10.100.12.36
JENKINS_UC=https://updates.jenkins.io
JENKINS_OPERATOR_HTTP_PROD_JENKINS_PORT_8080_TCP_ADDR=10.100.12.36
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_PORT_50000_TCP_ADDR=10.96.96.83
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_SERVICE_PORT=50000
SHLVL=2
KUBERNETES_PORT_443_TCP_PROTO=tcp
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_PORT_50000_TCP_PORT=50000
JENKINS_HOME=/var/lib/jenkins
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
REF=/usr/share/jenkins/ref
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
JENKINS_OPERATOR_HTTP_PROD_JENKINS_PORT=tcp://10.100.12.36:8080
PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
JENKINS_OPERATOR_SLAVE_PROD_JENKINS_SERVICE_HOST=10.96.96.83
DEBUG_JENKINS_OPERATOR=true
_=/usr/bin/env
total 8
drwxrwxrwx 2 root root 4096 Mar  4 05:33 .
drwxr-xr-x 1 root root 4096 Mar  4 05:33 ..
+ ls -la /var/lib/jenkins
Printing debug messages - end
+ echo 'Printing debug messages - end'
+ mkdir -p /var/lib/jenkins/init.groovy.d
+ cp -n /var/jenkins/init-configuration/createOperatorUser.groovy /var/lib/jenkins/init.groovy.d
+ mkdir -p /var/lib/jenkins/scripts
+ cp /var/jenkins/scripts/init.sh /var/jenkins/scripts/install-plugins.sh /var/lib/jenkins/scripts
+ chmod +x /var/lib/jenkins/scripts/init.sh /var/lib/jenkins/scripts/install-plugins.sh
Installing plugins required by Operator - begin
+ echo 'Installing plugins required by Operator - begin'
+ cat
+ [[ -z '' ]]
+ install-plugins.sh
WARN: install-plugins.sh has been removed, please switch to jenkins-plugin-cli
```

В логах оператора вот такое по кругу:
```shell
2023-03-04T05:35:43.793Z        DEBUG   controller-jenkins      createServiceAccount with annotations map[]     {"cr": "prod-jenkins"}
2023-03-04T05:35:43.884Z        DEBUG   controller-jenkins      Service account, role and role binding are present      {"cr": "prod-jenkins"}
2023-03-04T05:35:43.884Z        DEBUG   controller-jenkins      Extra role bindings are present {"cr": "prod-jenkins"}
2023-03-04T05:35:43.887Z        DEBUG   controller-jenkins      Jenkins HTTP Service is present {"cr": "prod-jenkins"}
2023-03-04T05:35:43.892Z        DEBUG   controller-jenkins      Jenkins slave Service is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:43.892Z        DEBUG   controller-jenkins      Kubernetes resources are present        {"cr": "prod-jenkins"}
2023-03-04T05:35:43.892Z        DEBUG   controller-jenkins      Jenkins master pod is present   {"cr": "prod-jenkins"}
2023-03-04T05:35:43.892Z        DEBUG   controller-jenkins      Jenkins master pod not ready    {"cr": "prod-jenkins"}
2023-03-04T05:35:45.753Z        DEBUG   controller-jenkins      Reconciling Jenkins     {"cr": "prod-jenkins"}
2023-03-04T05:35:45.753Z        DEBUG   controller-jenkins      Operator credentials secret is present  {"cr": "prod-jenkins"}
2023-03-04T05:35:45.781Z        DEBUG   controller-jenkins      Scripts config map is present   {"cr": "prod-jenkins"}
2023-03-04T05:35:45.794Z        DEBUG   controller-jenkins      Init configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:45.854Z        DEBUG   controller-jenkins      Base configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:45.854Z        DEBUG   controller-jenkins      GroovyScripts Secret and ConfigMap added watched labels {"cr": "prod-jenkins"}
2023-03-04T05:35:45.854Z        DEBUG   controller-jenkins      ConfigurationAsCode Secret and ConfigMap added watched labels   {"cr": "prod-jenkins"}
2023-03-04T05:35:45.854Z        DEBUG   controller-jenkins      createServiceAccount with annotations map[]     {"cr": "prod-jenkins"}
2023-03-04T05:35:45.941Z        DEBUG   controller-jenkins      Service account, role and role binding are present      {"cr": "prod-jenkins"}
2023-03-04T05:35:45.942Z        DEBUG   controller-jenkins      Extra role bindings are present {"cr": "prod-jenkins"}
2023-03-04T05:35:45.946Z        DEBUG   controller-jenkins      Jenkins HTTP Service is present {"cr": "prod-jenkins"}
2023-03-04T05:35:45.951Z        DEBUG   controller-jenkins      Jenkins slave Service is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:45.951Z        DEBUG   controller-jenkins      Kubernetes resources are present        {"cr": "prod-jenkins"}
2023-03-04T05:35:45.952Z        INFO    controller-jenkins      Jenkins master pod restarted by kubernetes: Invalid Jenkins pod phase '{Phase:Failed Conditions:[{Type:Initialized Status:True LastProbeTime:0001-01-01 00:00:00 +0000 UTC LastTransitionTime:2023-03-04 05:35:40 +0000 UTC Reason: Message:} {Type:Ready Status:False LastProbeTime:0001-01-01 00:00:00 +0000 UTC LastTransitionTime:2023-03-04 05:35:40 +0000 UTC Reason:PodFailed Message:} {Type:ContainersReady Status:False LastProbeTime:0001-01-01 00:00:00 +0000 UTC LastTransitionTime:2023-03-04 05:35:40 +0000 UTC Reason:PodFailed Message:} {Type:PodScheduled Status:True LastProbeTime:0001-01-01 00:00:00 +0000 UTC LastTransitionTime:2023-03-04 05:35:40 +0000 UTC Reason: Message:}] Message: Reason: NominatedNodeName: HostIP:192.168.39.186 PodIP:172.17.0.4 PodIPs:[{IP:172.17.0.4}] StartTime:2023-03-04 05:35:40 +0000 UTC InitContainerStatuses:[] ContainerStatuses:[{Name:jenkins-master State:{Waiting:nil Running:nil Terminated:&ContainerStateTerminated{ExitCode:1,Signal:0,Reason:Error,Message:,StartedAt:2023-03-04 05:35:43 +0000 UTC,FinishedAt:2023-03-04 05:35:43 +0000 UTC,ContainerID:docker://d5bcaf5ca4f052984d79e8592832fd6a77a7b796109e85f9fba7d1a539a393d0,}} LastTerminationState:{Waiting:nil Running:nil Terminated:nil} Ready:false RestartCount:0 Image:jenkins/jenkins:2.375.3-lts-alpine ImageID:docker-pullable://jenkins/jenkins@sha256:20c119aa8d9346fea1643d80d32d18cd2ac28464fa557596688aaf022e808596 ContainerID:docker://d5bcaf5ca4f052984d79e8592832fd6a77a7b796109e85f9fba7d1a539a393d0 Started:0xc0014c363d}] QOSClass:Burstable EphemeralContainerStatuses:[]}'       {"cr": "prod-jenkins"}
2023-03-04T05:35:45.972Z        DEBUG   controller-jenkins      Reconciling Jenkins     {"cr": "prod-jenkins"}
2023-03-04T05:35:45.972Z        DEBUG   controller-jenkins      Operator credentials secret is present  {"cr": "prod-jenkins"}
2023-03-04T05:35:45.998Z        DEBUG   controller-jenkins      Scripts config map is present   {"cr": "prod-jenkins"}
2023-03-04T05:35:46.011Z        DEBUG   controller-jenkins      Init configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.034Z        DEBUG   controller-jenkins      Base configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.035Z        DEBUG   controller-jenkins      GroovyScripts Secret and ConfigMap added watched labels {"cr": "prod-jenkins"}
2023-03-04T05:35:46.035Z        DEBUG   controller-jenkins      ConfigurationAsCode Secret and ConfigMap added watched labels   {"cr": "prod-jenkins"}
2023-03-04T05:35:46.035Z        DEBUG   controller-jenkins      createServiceAccount with annotations map[]     {"cr": "prod-jenkins"}
2023-03-04T05:35:46.126Z        DEBUG   controller-jenkins      Service account, role and role binding are present      {"cr": "prod-jenkins"}
2023-03-04T05:35:46.127Z        DEBUG   controller-jenkins      Extra role bindings are present {"cr": "prod-jenkins"}
2023-03-04T05:35:46.133Z        DEBUG   controller-jenkins      Jenkins HTTP Service is present {"cr": "prod-jenkins"}
2023-03-04T05:35:46.142Z        DEBUG   controller-jenkins      Jenkins slave Service is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.142Z        DEBUG   controller-jenkins      Kubernetes resources are present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.142Z        INFO    controller-jenkins      Creating a new Jenkins Master Pod default/jenkins-prod-jenkins  {"cr": "prod-jenkins"}
2023-03-04T05:35:46.221Z        DEBUG   controller-jenkins      Reconciling Jenkins     {"cr": "prod-jenkins"}
2023-03-04T05:35:46.221Z        DEBUG   controller-jenkins      Operator credentials secret is present  {"cr": "prod-jenkins"}
2023-03-04T05:35:46.232Z        DEBUG   controller-jenkins      Scripts config map is present   {"cr": "prod-jenkins"}
2023-03-04T05:35:46.246Z        DEBUG   controller-jenkins      Init configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.260Z        DEBUG   controller-jenkins      Base configuration config map is present        {"cr": "prod-jenkins"}
2023-03-04T05:35:46.260Z        DEBUG   controller-jenkins      GroovyScripts Secret and ConfigMap added watched labels {"cr": "prod-jenkins"}
2023-03-04T05:35:46.260Z        DEBUG   controller-jenkins      ConfigurationAsCode Secret and ConfigMap added watched labels   {"cr": "prod-jenkins"}
2023-03-04T05:35:46.260Z        DEBUG   controller-jenkins      createServiceAccount with annotations map[]     {"cr": "prod-jenkins"}
```

менял образы jenkins добавлял настройки, про отсутствие которых были сообщения в логах они касались настройки CasC плагина в jenkins-instance-pavan.yaml
