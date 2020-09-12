# ansible-nginx-k8s

Configure a basic kubernetes cluster. 

## Prep
```
ansible-galaxy install -r requirements.yml
```

## PowerCLI

```foreach($vm in "kubectl", "kuben01", "kuben02", "harbor"){Get-VM $vm}```

```foreach($vm in "kubectl", "kuben01", "kuben02", "harbor"){Start-VM -VM $vm -Confirm:$false}```

```foreach($vm in "kubectl", "kuben01", "kuben02", "harbor"){Get-Snapshot -VM $vm | where {$_.IsCurrent -eq $true}}```

```foreach($vm in "kubectl", "kuben01", "kuben02", "harbor"){$snap=Get-Snapshot -VM $vm | where {$_.IsCurrent -eq $true};Set-VM -VM $vm -SnapShot $snap -Confirm:$false}```

## Reset controller
```
kubeadm reset
rm -fR .kube
iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X
```

## Access cluster
```
mkdir .kube
ssh kubectl.lab.leyux.org "sudo cat /etc/kubernetes/admin.conf" > .kube/config
```

## Create secret for harbor
```
kubectl create secret docker-registry harbor \
--docker-server=https://harbor.lab.leyux.org \
--docker-username=admin \
--docker-email=c.leygraf@f5.com \
--docker-password='F5Ng1nx!'
```
Login
```
docker login harbor.lab.leyux.org
```
Pull, tg and push sample image
```
docker pull gcr.io/kuar-demo/kuard-amd64:1
docker tag gcr.io/kuar-demo/kuard-amd64:1 harbor.lab.leyux.org/nginx-kic/kuard:v1
docker push harbor.lab.leyux.org/nginx-kic/kuard:v1
docker pull harbor.lab.leyux.org/nginx-kic/kuard:v1
```
Test deployment of kuard
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
  labels:
    app: kuard
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kurad
  template:
    metadata:
      labels:
        app: kuard
    spec:
      containers:
      - name: kuard
        image: harbor.lab.leyux.org/nginx-kic/kuard:v1
      imagePullSecrets:
      - name: harbor
```