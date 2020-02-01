1. sebelum mendeploy kubernetes di aws menggunakan kops, anda diwajibkan terlebih dahulu memiliki
sebuah domain. Saya sendiri sudah memiliki domain bernama ijal.tech.

nantinya domain cluster saya akan bernama kubernetes.ijal.tech, lalu untuk api nya
akan bernama api.kubernetes.ijal.tech

setelah anda memiliki domain, langkah selanjutnya adalah membuat hosted zone di aws,
dengan command: `aws route53 create-hosted-zone --name dev.example.com --caller-reference 1`

untuk cek apakah domain sudah up atau belum:
`dig NS ijal.tech`
hasilnya:
```
;; ANSWER SECTION:
ijal.tech.		6411	IN	NS	ns2.dns-parking.com.
ijal.tech.		6411	IN	NS	ns1.dns-parking.com.
```

`dig NS kubernetes.ijal.tech`
hasilnya:
```
;; ANSWER SECTION:
kubernetes.ijal.tech.	6389	IN	NS	ns-639.awsdns-15.net.
kubernetes.ijal.tech.	6389	IN	NS	ns-458.awsdns-57.com.
kubernetes.ijal.tech.	6389	IN	NS	ns-1538.awsdns-00.co.uk.
kubernetes.ijal.tech.	6389	IN	NS	ns-1504.awsdns-60.org.
```

2. membuat s3 bucket untuk menyimpan state (default region saya adalah singapore):
`aws s3 mb s3://clusters.kubernetes.ijal.tech`

3. build cluster configuration:
```
kops create cluster --name kubernetes.ijal.tech \
--state s3://clusters.kubernetes.ijal.tech \
--zones ap-southeast-1a \
--node-count=2 \
--node-size t2.micro \
--master-size t2.micro \
--dns-zone kubernetes.ijal.tech
```

`kops update cluster kubernetes.ijal.tech --yes --state s3://clusters.kubernetes.ijal.tech`

4. validate cluster: `kops validate cluster --state s3://clusters.kubernetes.ijal.tech`

5. check if nodes are up: `kubectl get nodes`

6. just for test:
```
kubectl run hello-minikube \
--image k8s.gcr.io/echoserver:1.4 \
--port 8080
```

`kubectl expose deployment hello-minikube --type NodePort`

`kubectl get services`

7. delete cluster:
```
kops delete cluster kubernetes.ijal.tech \
--state s3://clusters.kubernetes.ijal.tech \
--yes
```

notes: 
ssh to the master: `ssh -i ~/.ssh/id_rsa admin@api.kubernetes.ijal.tech`
