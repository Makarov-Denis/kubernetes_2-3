# Домашнее задание к занятию "`Конфигурация приложений`" - `Макаров Денис`



---

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба контейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

####  Решение

Создадим namespace задания представлен на скриншоте ниже:

![ns kubdz2-3](https://github.com/user-attachments/assets/ef413804-be75-435c-998e-49a6b940214f)

Service представлен на скриншоте ниже:

![service](https://github.com/user-attachments/assets/8e2dd3cc-ebdb-47f0-9dfd-65a417c7fbfd)

Поднимаем сервис представлен на скриншоте ниже:

![apply service](https://github.com/user-attachments/assets/80b10581-4508-4eba-abaf-4ba3438c2aed)

ConfigMap представлен на скриншоте ниже:

![configmap](https://github.com/user-attachments/assets/42c16b8d-eb3e-423f-a9c8-52726835f517)


Поднимаем configMap представлено на скриншоте ниже:

![apply config map](https://github.com/user-attachments/assets/814a39e1-dd9e-4490-9e5c-75048a1bcf5c)

Deployment представлен на скриншоте ниже:

![deployment](https://github.com/user-attachments/assets/8a33665e-2a66-48cc-adcb-d94def04d567)

Поднимаем deployment представлено на скриншоте ниже:

![apply deployment](https://github.com/user-attachments/assets/e8774aa5-a0da-43de-b752-b57492ee1f47)

![describe deployment1](https://github.com/user-attachments/assets/c7bb515c-c0a7-4d31-97f1-21c56e8fe744)

Проверяем curl представлено на скриншотах ниже:

![curl 32000](https://github.com/user-attachments/assets/71a2320a-f3ce-4942-a16e-9a15b08f7f82)

![curl 32001](https://github.com/user-attachments/assets/40a54465-9e8b-4ab7-ab51-88fdac0e569b)

И через браузер:

![curl 32000 brose](https://github.com/user-attachments/assets/afbe211a-c23d-4889-aeb5-c15efc652725)

![curl 32001 brose](https://github.com/user-attachments/assets/3339a690-758a-42ec-965d-d91cfc0b6d2e)

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.

[deployment_https.yaml](scr%2Fnginx_HTTPS%2Fdeployment_https.yaml)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: netology-deployment
  namespace: dz2-3
  labels:
    app: main
spec:
  replicas: 1
  selector:
    matchLabels:
      app: main
  template:
    metadata:
      labels:
        app: main
    spec:
      containers:
      - name: nginx
        image: nginx:1.19.2
        volumeMounts:
          - name: nginx-index-file
            mountPath: /usr/share/nginx/html/    

      volumes:
        - name: nginx-index-file
          configMap:
            name: configmap-nginx-multitool
```
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.

[configmap_https.yaml](scr%2Fnginx_HTTPS%2Fconfigmap_https.yaml)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: configmap-nginx-multitool
  namespace: dz2-3
data:
  index.html: |
    <html>
    <h1>Welcome</h1>
    </br>
    <h1>This is a configMap Index file</h1>
    </html>
```
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
```bash
root@netology-01:~# openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout tls.key -out tls.crt -subj "/CN=my-app.com" -days 365
Generating a RSA private key
.................................................................................++++
..........................................................++++
writing new private key to 'tls.key'
-----

root@netology-01:~# ls
snap  tls.crt  tls.key
```
- [tls.crt](scr%2Fnginx_HTTPS%2Fcert%2Ftls.crt)
- [tls.key](scr%2Fnginx_HTTPS%2Fcert%2Ftls.key)

4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстрировать доступ к приложению по HTTPS.
Создаем ```secret```:
```bash
nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl create secret tls secret-tls --cert=cert/tls.crt --key=cert/tls.key --namespace=dz2-3
secret/secret-tls created
```
```bash
nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl get secret -o yaml -n dz2-3
```
```yaml
apiVersion: v1
items:
- apiVersion: v1
  data:
    tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUZDekNDQXZPZ0F3SUJBZ0lVZkZsS0ROMlBjeG90Z3hHb1pDZ0Zodks2VjMwd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0ZURVRNQkVHQTFVRUF3d0tiWGt0WVhCd0xtTnZiVEFlRncweU5EQXlNalV4TnpBM01EbGFGdzB5TlRBeQpNalF4TnpBM01EbGFNQlV4RXpBUkJnTlZCQU1NQ20xNUxXRndjQzVqYjIwd2dnSWlNQTBHQ1NxR1NJYjNEUUVCCkFRVUFBNElDRHdBd2dnSUtBb0lDQVFEcUNwYS9GOTcrS0NkZGZkWllwZXBTdDR0N3E4eGlJdElCSFVCY25RNm8KOVJQL1c1TnhIdGhkUWo2RzIycjlyTjdaM0lUM3hwN0dpWXFNdjZvTVk3RUpZbmY1SWZqMVEyU0pCM1ZSd0VKUwpkS2J5R1BMcUp2UTdtcmE2YlpkOVlVbkE5WmNKL0VtcHdwaW8zdFp1amZMblh1ZFcwcWZuSWNyMXhkQ0JabUg1Ck9TOTBTSVQvQXpiMFZ6Sno5OFFVMWxnSTA1TVgrQ0JXaFlqVEZlaVdON1BoV2pNS3hEb2pIMDVFZUt5R2JLTFYKaExFSC9vd1l4ampVVEt4U2hocDhyQUNwWjdxeFVkRi9zbkJ5QUFSMTFQMVVubHF6R0JXeTRNQVIya2hneVNITwo5ekhabHZsMmp1N2xnSjFON3JHMjRtTy9lRHlvaVVmYlhlVlZIVEhLRm1qTmxKWm5QaG9CNm1TcHY1K3N2ZHpZCnVWWSt2NytuQlRWaXV5aHVMK0lvL1lRdVp6WWZ1M3AybDdvWVM4L2dkRlBPYitGR0Z1c3VsWVY1QmtHTWRDY1AKV2FSbmRUaml3WnRHaXlSUWhjUTZtNHEyT0NIa3VqZXRoaFNNcitDYWZoK0FvUzlQOUp2a0ZDNFBGUjl4UHlpagowU1lCdHNLaVI3NXgzSXdzY0IzdGl2N3FNYS9HdHJaaWZzU1NjS1FjaXlFckxLQ3VVT2UzMkgvZ3RINmRkSUlMClpNaDRIeExaYWVZeWxtY0I5OHJESE5uMmtZWEV3VkIrVG5sZmtVcWt6dWJSVVBkQnNaUG9RZlpOcTFvaDJXVU8KUGtKWHAyb2NuMVJsUlVJMVdKc1Z2WUhhRERIUWlTRC9ibFBUZDNLa1RiZTRkMnVLaXNZeERKdVd2MmdBZzVGUgpPUUlEQVFBQm8xTXdVVEFkQmdOVkhRNEVGZ1FVZ21uemJCQkZsd1BicmVhNGJiUWxQUDlEdno4d0h3WURWUjBqCkJCZ3dGb0FVZ21uemJCQkZsd1BicmVhNGJiUWxQUDlEdno4d0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBTkJna3EKaGtpRzl3MEJBUXNGQUFPQ0FnRUFSTW11MXFBcG5OT1diejZmbWtTWTY1MEZxZ0QwTTRmNHV5bG9Oc2xtYi9mMQpZZDFHOUVORTg2ZVIxempvZkFyZnZSQnduSCs3eWNIczZHUnBOMzJQbVd0UjBOSGsyVDNQYjYwY3hqZWhrY28zCjdGREl1YlRvK0FJZmJEWFAzMnJaMUdsZWMzWjRCTVJhWVNNVVJtZlJIK3JUdlBES01OMSsxRnNES3E5Zjg3eUQKeHVoUlA0TjU2WCtEN0wzdGtxQmRkM3RLUnBUdEpSVzJ6QnIwUWNGNTIrMmRaR2l2UG9LVzN6SzFsN040Yk1SRwppZndtcjE3bXVqWFRIMFN3ZytnS05xUXBUUDFRcnJ6cXkzWURLSXU3NnJZaDlEbG9Mc0dDVFlwalhwbUVVUUpCCnBhbjdKYjlDUUtrQ0N4ZDlyUTJ2NHBlK1EwM3llOGFaTlBFdzNVcjFrbllSekdiTmlCdE9GdmVWNzJGa0ZmaTMKd1F1SmJiMWt0MkhBY05WRUdRMW1SR2E1aU1TNGpuaG9qYTZkZzI5SUZpcHRlM0szZVlRbEg0T1VIR21Na0xsNApodFZZbURIUWpCL1p3WTJhamtNZzZuU3pFUStqdHZTeWVUd0RLY1FvbHdsREpWVWtjUGpQSGg4c3d3YmwrUEVTClpSNGZRNXA1SlNPUGpLMGNZeGhGcy9hWjV6Z0pFbXJjYTVUQ2xKKzRpdzU4cmw1dElDdlhLbWVvWGNlN2FaTlEKbGZ1b1RLdDVtRE5RN3d4Qm5yaE5CRUZ2T2Jud1BjbG53dlhxUVBBRE9IQUZrQW12UHR2SGszdEYvdFFCU2NEbgpsN2dmcTZQUDViWkdneG9IQklBbTU0VnhZTDlkUUNHKzB0TVE4cE5jMEFtZktVWlkreHppMEtUUUQvLzN1dTQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUpSQUlCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQ1M0d2dna3FBZ0VBQW9JQ0FRRHFDcGEvRjk3K0tDZGQKZmRaWXBlcFN0NHQ3cTh4aUl0SUJIVUJjblE2bzlSUC9XNU54SHRoZFFqNkcyMnI5ck43WjNJVDN4cDdHaVlxTQp2Nm9NWTdFSlluZjVJZmoxUTJTSkIzVlJ3RUpTZEtieUdQTHFKdlE3bXJhNmJaZDlZVW5BOVpjSi9FbXB3cGlvCjN0WnVqZkxuWHVkVzBxZm5JY3IxeGRDQlptSDVPUzkwU0lUL0F6YjBWekp6OThRVTFsZ0kwNU1YK0NCV2hZalQKRmVpV043UGhXak1LeERvakgwNUVlS3lHYktMVmhMRUgvb3dZeGpqVVRLeFNoaHA4ckFDcFo3cXhVZEYvc25CeQpBQVIxMVAxVW5scXpHQld5NE1BUjJraGd5U0hPOXpIWmx2bDJqdTdsZ0oxTjdyRzI0bU8vZUR5b2lVZmJYZVZWCkhUSEtGbWpObEpablBob0I2bVNwdjUrc3Zkell1VlkrdjcrbkJUVml1eWh1TCtJby9ZUXVaellmdTNwMmw3b1kKUzgvZ2RGUE9iK0ZHRnVzdWxZVjVCa0dNZENjUFdhUm5kVGppd1p0R2l5UlFoY1E2bTRxMk9DSGt1amV0aGhTTQpyK0NhZmgrQW9TOVA5SnZrRkM0UEZSOXhQeWlqMFNZQnRzS2lSNzV4M0l3c2NCM3RpdjdxTWEvR3RyWmlmc1NTCmNLUWNpeUVyTEtDdVVPZTMySC9ndEg2ZGRJSUxaTWg0SHhMWmFlWXlsbWNCOThyREhObjJrWVhFd1ZCK1RubGYKa1Vxa3p1YlJVUGRCc1pQb1FmWk5xMW9oMldVT1BrSlhwMm9jbjFSbFJVSTFXSnNWdllIYURESFFpU0QvYmxQVApkM0trVGJlNGQydUtpc1l4REp1V3YyZ0FnNUZST1FJREFRQUJBb0lDQUN2a2puOEtPQTBNZE0ySTR5RS9CS0k4CndCRVNtRU16YXBWQTZpZzBZR0o1akNXUkJDYnI5UUlRZ1crRFNSNklSRWN4bjFKazByUkRhVk9hUW9jT1QwNkcKUkIvYUtqbTlTT2FXR24rWmdoYTZ2L0Naa3owc3p4TTZvZGgyNHpsbGZKS092S1BueDl6cG5QM1d4UHA0N3J4TAp4VEU0VXJyN1VIZ2xnRVEwY2wxdVJ5TVUwclNNNHNxU2EranA1OEZNcmJnQ0RnMHB3TTdaUGw3d05lMnVSck1WCjJvckRZRy9qMkNicFJ0bnpGOXJaaHVZTDdEUmRRSjA0UC8wK0gwdVFhcE5hMjkyVGphbllTbFJuQW04aTRkdnoKMHVqUnRJZ1d0STdrbER4cW5FZVhmcWJqTktmeWlJVk1TTFFyOXZKb3BQSnMxMXQ0VzV5ZGtId1UvSmg4K1d4eApCTEM0M1FEQ1ZKaU5iWWx1T1pzVlM0SnZsbGU4ajUzWFlmZ0tkRktPWVFFOUFCNnJWem9NL1pSbjR1N29RbzNtCnpHRi9CZm4rdnVsTWlNb0VxRElRMS9Db0VSaGM1OEFUbllwbnBRNU1GMVdYM0ZuUHhqeitJSTU5UTVnNm91SXAKWlk2L1pFZG1qbjVVMGxFVm9Qa2pVQmJwcnBPYjN1U05jSkVXUUt6SlNnZkVCR05DNEZaRFdDUnh0azRSNC9NNAp0YzBzWDkxMGg0WWNvQmRvam1oQlpsWDQybWlUTDJHcjcrd2JpeXE1QVYyRTRsejR6NGRjM0YvVThxekxVb215Cnp6dVhYVEJTa0pHeEcvQmJNaStpcXNNemQ4RHNmd2ZGOFlvVGNlR1ErdnJvNXprc3FhMWlwODQ2cTFjVnl4Q2YKZXlDc2tVL3dObG5DS2pXOTFMNkJBb0lCQVFENUtrM2pRYWJlRzcrbVpMb0R3Z1FuMjg2eGpTSkNjYzIyN3pCUwozWCtLbS9CL2ZIdzR0c1AvcndDV2pob1VUOS8zZ2tMYkZGUkdHK3Y4aXF6RXhXMDh2aHFjS0VuVUZGcW44SWNuCkxDWnN1UTVXZkNqeUppdjNBeExuaHRSalh2NnhBYXhpcC9VRkxhc3c5VXJRb2lxUE1LNk14QjBSUGIrS0Fja0YKVklGaStEeU4wU0xyZ2F4OVYzU25xNFJkY3ZiMExOOFdEb3lodGEyQlh5K1dDY0pLWkJtaTUwaXNQRVk0ck1ESgpFRGRQU3pMUDR1QmtCVHc0eTQzYlFIYU5sRS9hOE5pUTNSakM4Q1NYM0U5TGUrc1pLWGpmWTUwMUNEY1N1dyt4CnBlNlJPK2oyc2dSaFFHVEVWbHhpQ3NFSmR0aTEreGNqb1pkWXJWVVNCeDZqMzhKeEFvSUJBUUR3ZGhUSUxhZTcKREJrdzA2bnY3SnNsbUsrWnRhV0JPeUM3ZnN5WExHdzlxSlBzL0x1K3l5cTB0ZkEvNTAwaE9yb3ZGMnQ0Z0lwRgo1VSs3SzFBZ3BCVEZobVdDSjFxTUZ4TDVlY1RqTi8vVW8reHNVckszOTVScjhMM2Z0OTJZRFdqZ1FDR2xBcG5xCmlKRlBwVTg4US9sVEdGL0ZGNHpEcFRNZURRaHhiNXc2aTlVR2ZQMisxNkJwenhqS0R6S1lVaGxoUm5nRXNhNlgKSkVvbU9kRmZkb28zV2hKNmxFMCt0MnhKWGJGbE93SmFrQ1kwMGFlSzFiM1dpSUJaWis4YW5kSDV6b3A3Y2xBZApvaWlmNXdRcUZ5YUFWbUQ1VTZpQkVGWFZoZldmWUlXOGIxbS9sSFVkN0s3OFIwM0FINFBFTFdsclFkVjlUZlJyCmRwUDYwWXRVNGs5SkFvSUJBUUR3VEpsamMveWZ0cmxGbTEwK3BJM25kdmpIaWFxaDFDbW1wTlhCQlRldEVTbUgKZWlJL2ZCeFk2WWt5cWdlQzBXblp4Ym4rbVlPUlBmcUF5NmxGK0hXYW9Hai9jMmVJYnJ6anZIaE1FaXRZcmJ5agpNZ2szU0JNY25jMU1sMThjR3hDYzIxVktyRnNFekgrT3J2S2hkZFIvMWw1eENlNVNvMituaElNL2JibC9IcE1mCjNyUEQxNExvTzBFWk43Um5mNm1sNGVTZzNCVkxHL0VpbFE5S3IweSsrLzB4ZThjOXZMK29od3RDbmk0SmZpZWEKRUYrQ2R2NFdkRkh5UXlCUytOZHUrdHFTRTNsKy82VDdCSkZBNWxqZEluOGRTbS9pSm5NZTBHT1pXOE5TTkNwQQpTWDBwNGJXTkdSRHR5UnRVcWxia1l5MTB3ckk0NXFubHdoSU56NDR4QW9JQkFRRHJPTWRaam5ldW5NWWpvbHB4ClRjWHpBQ3ArdFZkQ1ZJSFBoOWxBNUg2NXppZHVRMGl3K2ZNN3RXSmdVTFo5bEFJL1FLeXJ3eW4vOTdLSUNIV28KaUhtZFE3d1dsc0tYbVpiQkhtSUFWMjVXSjBpR0tsdVRaSWYzRXhmYU9mVjE0V0EvUmR6am11alBxV3BrTy9TSApvb0xKeTJVYjJzNmpMLzRTSG5PczY1NHJFMUIrdVZSTEZJbGlGK2xLOTVUcHRoNEhyelNHZXYycjhoN3F4OUpOCmpScmx6S0dZOFd1aXR6RWhqNXFSeVNpalNMRm5KOU82Rng1T04xYytubElpZWxIR0NVb2tPZzJ1LzBxNEtQZEUKNlVLTGRuUUVVZGJhOGd6VkErYmpVanRndXBoVHRUamYzZ0RLM2tGcmVDaWdoai9DWVRNakVWZlFxNzFVTlJrVQpIeEdwQW9JQkFRRHlqRis2dkVJN3hxRk9SME4rTUZPZ2duNi9Ma01qZW1wd2ZxRUJ3WGVpOGh4ZVN0K3l2OU1aClJ4bFVnSExNc2M5VVB2K1hFaXkrMG4xN3ZJL0U2QXdNWk5IY3dBUUNBMkJSenA5R1E2QWNsV1dPNnVxSmpiVTEKWTJwSnpUdm15aTJYSjdqQUtCZHlveHZjT1RwSHBYdTBVaFZJclByMExJeXdVc0ZubzR6eGtLMjBiR2l1OWVheQpONE1tdEZUK0IrcE9zN3VvUFF1NmtCSDVEQy9Ld3BaS1BDZUdPS0RvRkhTWGJxaHZVYW1xaGRVQ3JuR0taWitCCkdsMForZm9IK1hBZFFZbTVoSGxKRGw0VEdlZVVvdmdVUmZ3dFMzNXphRG5yMjZ4TDBsNHhQdG41cVY0M2VjN1oKOU0reGdjU2VqUUt5K25oZ3VQRFRkSmdSeDIyTkFYVGEKLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQo=
  kind: Secret
  metadata:
    creationTimestamp: "2024-02-25T17:28:33Z"
    name: secret-tls
    namespace: dz2-3
    resourceVersion: "509279"
    uid: a4b9e015-13d4-44a9-a6ee-bb9b730e43ef
  type: kubernetes.io/tls
kind: List
metadata:
  resourceVersion: ""
```
Ingress: [ingress_https.yaml](scr%2Fnginx_HTTPS%2Fingress_https.yaml)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-https
  namespace: dz2-3
spec:
  rules:
    - host: my-app.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: service-https
                port:
                  number: 80

  tls:
    - hosts:
      - my-app.com
      secretName: secret-tls
```
Service: [service_https.yaml](scr%2Fnginx_HTTPS%2Fservice_https.yaml)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-https
  namespace: dz2-3
spec:
  type: NodePort
  ports:
    - name: http-nginx
      port:  80
      nodePort: 32000
      protocol: TCP
      targetPort: 80
  selector:
    app: main

```
Запускаем все и проверяем:
```bash
nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl apply -f configmap_https.yaml 
configmap/configmap-nginx-multitool created

nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl apply -f service_https.yaml 
service/service-https created

nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl apply -f ingress_https.yaml 
ingress.networking.k8s.io/ingress-https created

nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl apply -f deployment_https.yaml 
deployment.apps/netology-deployment created

nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ kubectl get all -n dz2-3
NAME                                       READY   STATUS    RESTARTS   AGE
pod/netology-deployment-5cdd68d99f-zskp8   1/1     Running   0          14s

NAME                    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/service-https   NodePort   10.152.183.245   <none>        80:32000/TCP   28s

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/netology-deployment   1/1     1            1           14s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/netology-deployment-5cdd68d99f   1         1         1       14s
```
Прописываем DNS my-app.com в файл ```hosts``` и проверяем доступность измененной страницы:
```bash
nikulinn@nikulin:~/other/kuber_2-3/scr/nginx_HTTPS$ curl -k https://my-app.com
<html>
<h1>Welcome</h1>
</br>
<h1>This is a configMap Index file</h1>
</html>
```
И в браузере:
![img_3.png](img%2Fimg_3.png)


5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

