# Домашнее задание к занятию "14.3 Карты конфигураций"

## Задача 1: Работа с картами конфигураций через утилиту kubectl в установленном minikube

Выполните приведённые команды в консоли. Получите вывод команд. Сохраните задачу 1 как справочный материал.

### Ответ:

Создание карты конфигураций
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl create configmap nginx-config --from-file=nginx.conf
configmap/nginx-config created
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl create configmap domain --from-literal=name=netology.ru
configmap/domain created
vagrant@vagrant:~/netology-14-security-03-maps$
```

Просмотр списка карт конфигураций
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmaps
NAME               DATA   AGE
domain             1      113s
kube-root-ca.crt   1      173d
nginx-config       1      2m6s
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap
NAME               DATA   AGE
domain             1      114s
kube-root-ca.crt   1      173d
nginx-config       1      2m7s
vagrant@vagrant:~/netology-14-security-03-maps$
```

Просмотр карт конфигураций
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap nginx-config
NAME           DATA   AGE
nginx-config   1      3m10s
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl describe configmap domain
Name:         domain
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
name:
----
netology.ru

BinaryData
====

Events:  <none>
vagrant@vagrant:~/netology-14-security-03-maps$
```

Получение информации в формате YAML и JSON
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap nginx-config -o yaml
apiVersion: v1
data:
  nginx.conf: |
    server {
        listen 80;
        server_name  netology.ru www.netology.ru;
        access_log  /var/log/nginx/domains/netology.ru-access.log  main;
        error_log   /var/log/nginx/domains/netology.ru-error.log info;
        location / {
            include proxy_params;
            proxy_pass http://10.10.10.10:8080/;
        }
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2023-01-28T18:41:00Z"
  name: nginx-config
  namespace: default
  resourceVersion: "85487"
  uid: d5c8055b-725a-4f14-baaf-6b40dbba8783
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap domain -o json
{
    "apiVersion": "v1",
    "data": {
        "name": "netology.ru"
    },
    "kind": "ConfigMap",
    "metadata": {
        "creationTimestamp": "2023-01-28T18:41:13Z",
        "name": "domain",
        "namespace": "default",
        "resourceVersion": "85498",
        "uid": "fef4e4b4-fee8-4c8a-8b33-47bdf177fe9d"
    }
}
vagrant@vagrant:~/netology-14-security-03-maps$
```

Выгрузка карты конфигурации и сохранение в файл
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmaps -o json > configmaps.json
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap nginx-config -o yaml > nginx-config.yml
vagrant@vagrant:~/netology-14-security-03-maps$ cat configmaps.json
{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "v1",
            "data": {
                "name": "netology.ru"
            },
            "kind": "ConfigMap",
            "metadata": {
                "creationTimestamp": "2023-01-28T18:41:13Z",
                "name": "domain",
                "namespace": "default",
                "resourceVersion": "85498",
                "uid": "fef4e4b4-fee8-4c8a-8b33-47bdf177fe9d"
            }
        },
        {
            "apiVersion": "v1",
            "data": {
                "ca.crt": "-----BEGIN CERTIFICATE-----\nMIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p\na3ViZUNBMB4XDTIyMDgwNzE3NTMyOFoXDTMyMDgwNTE3NTMyOFowFTETMBEGA1UE\nAxMKbWluaWt1YmVDQTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALTc\n3cNurMumH8WN5Hu+8qTEAWPaYeVbdNgavcxkTHuDHX+gpd30HVVB2PdZY/EQwKLA\neY+Z9UJIUZTOcryZF43QEnFu/2PalF6suLLTmo02EJf/z83eBRc3UceDMv2hrokc\nVnww+rztGGpBz5tIt66vA99tEDE///mtnQzYErY1IYKUpHx+sjtZCaT1vgbWnpNq\nvWLEw9oMmIWTsDW/SrVqzQjsSeGOnY0y0AuCTC2ZUSFlapQ2AWm48STPkYOAQI+O\n+Ei0UZPrA5prunJRhAx0IuJEky0TQyY3lEULTSJsw0w4q5AQ4O3SDSrhS9+vRyd0\nFDd5corxyNhSuzDkXAkCAwEAAaNhMF8wDgYDVR0PAQH/BAQDAgKkMB0GA1UdJQQW\nMBQGCCsGAQUFBwMCBggrBgEFBQcDATAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQW\nBBTrsvnbivxn3ETLHftguX8WtsH/sjANBgkqhkiG9w0BAQsFAAOCAQEAX07BIFpM\ngNyeY48a4kSoUR3juqqG9ac9joLQzr4efPLI6S8+tMLrWNckzL7RvqvOIO/7Ogfp\nRtFnbNz2LG/+82uSQkaicZlUDCde+gBEuQecJq/nq9lvGA8gbUv/vA6OP4g3KjhD\n/J7WxpGVgx0TEvy3R5DNu3HbwqNrm1ZAoC/dLftoDC1u+DSV2t8XUAHrHrlyhDF8\nxSvWkXNj0JKgPQekvhy2ttmFFNZGP1BVI/SqdhMb2sGIwk9eupUmA72xoXAr/J44\n+d381akD5Kvtt56Dp5lP/fPhe44clLLv6PpKfVo5VyI5EPqGNgqSclczb4RtVd3+\n3NveZnJEH2vsfA==\n-----END CERTIFICATE-----\n"
            },
            "kind": "ConfigMap",
            "metadata": {
                "annotations": {
                    "kubernetes.io/description": "Contains a CA bundle that can be used to verify the kube-apiserver when using internal endpoints such as the internal service IP or kubernetes.default.svc. No other usage is guaranteed across distributions of Kubernetes clusters."
                },
                "creationTimestamp": "2022-08-08T17:53:55Z",
                "name": "kube-root-ca.crt",
                "namespace": "default",
                "resourceVersion": "335",
                "uid": "24a23581-a8f7-4065-8412-842d4941c20e"
            }
        },
        {
            "apiVersion": "v1",
            "data": {
                "nginx.conf": "server {\n    listen 80;\n    server_name  netology.ru www.netology.ru;\n    access_log  /var/log/nginx/domains/netology.ru-access.log  main;\n    error_log   /var/log/nginx/domains/netology.ru-error.log info;\n    location / {\n        include proxy_params;\n        proxy_pass http://10.10.10.10:8080/;\n    }\n}\n"
            },
            "kind": "ConfigMap",
            "metadata": {
                "creationTimestamp": "2023-01-28T18:41:00Z",
                "name": "nginx-config",
                "namespace": "default",
                "resourceVersion": "85487",
                "uid": "d5c8055b-725a-4f14-baaf-6b40dbba8783"
            }
        }
    ],
    "kind": "List",
    "metadata": {
        "resourceVersion": ""
    }
}
vagrant@vagrant:~/netology-14-security-03-maps$ cat nginx-config.yml
apiVersion: v1
data:
  nginx.conf: |
    server {
        listen 80;
        server_name  netology.ru www.netology.ru;
        access_log  /var/log/nginx/domains/netology.ru-access.log  main;
        error_log   /var/log/nginx/domains/netology.ru-error.log info;
        location / {
            include proxy_params;
            proxy_pass http://10.10.10.10:8080/;
        }
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2023-01-28T18:41:00Z"
  name: nginx-config
  namespace: default
  resourceVersion: "85487"
  uid: d5c8055b-725a-4f14-baaf-6b40dbba8783
vagrant@vagrant:~/netology-14-security-03-maps$
```

Удаление карты конфигурации
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl delete configmap nginx-config
configmap "nginx-config" deleted
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap nginx-config
Error from server (NotFound): configmaps "nginx-config" not found
```

Загрузка карты конфигурации из файла
```
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl apply -f nginx-config.yml
configmap/nginx-config created
vagrant@vagrant:~/netology-14-security-03-maps$ kubectl get configmap nginx-config
NAME           DATA   AGE
nginx-config   1      3s
vagrant@vagrant:~/netology-14-security-03-maps$
```

