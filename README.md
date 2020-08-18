# ansible-nginx-k8s

Configure a basic kubernetes cluster. 

## Prep
```
ansible-galaxy install -r requirements.yml
```

## PowerCLI

`foreach($vm in "kubectl", "kuben01", "kuben02"){Get-VM $vm}`

`foreach($vm in "kubectl", "kuben01", "kuben02"){Start-VM -VM $vm -Confirm:$false}`

`foreach($vm in "kubectl", "kuben01", "kuben02"){Get-Snapshot -VM $vm | where {$_.IsCurrent -eq $true}}`

`foreach($vm in "kubectl", "kuben01", "kuben02"){$snap=Get-Snapshot -VM $vm | where {$_.IsCurrent -eq $true};Set-VM -VM $vm -SnapShot $snap -Confirm:$false}`

## Reset controller
kubeadm reset
rm -fR .kube
iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X

## Access cluster
ssh kubectl "sudo cat /etc/kubernetes/admin.conf" > .kube/config