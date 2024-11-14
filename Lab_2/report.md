
University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024/2025
Group: K4111c
Author: Sorokin Nikita Alekseevich
Lab: Lab2
Date of create: 2.11.2024
Date of finished: 2.11.2024

---

Опишем манифесты 
*deployment.yml*
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image:  ifilyaninitmo/itdt-contained-frontend:master
        resources:
          limits:
            memory: "512Mi"
            cpu: "1"
        env:
          - name: REACT_APP_USERNAME
	        value: Sorokin
          - name: REACT_APP_COMPANY_NAME
	        value: ITMO
        ports:
        - containerPort: 3000
```

service.yml
```
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
  - port: 3000
    targetPort: 3000
```

В манифесте *deployment.yml* указано количество создаваемых реплик в параметре `replicas`. В разделе `env` указаны переменные окружения требуемые для разворачиваемого приложения 

Запустим кластер
```
kubectl apply -f deployment.yml
```

```
deployment.apps/frontend created
```

```
kubectl apply -f service.yml
```

```
service/frontend created
```

Проверим кластер:
```
kubectl get deployments
```

```
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
frontend   2/2     2            2           83s
```

```
kubectl get pods
```

```
NAME                       READY   STATUS    RESTARTS      AGE
frontend-c8f59c65b-2pr6h   1/1     Running   0             118s
frontend-c8f59c65b-78b4f   1/1     Running   0             118s
```

```
kubectl get services
```

```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
frontend      NodePort    10.97.255.174   <none>        3000:31225/TCP   2m5s
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP          60m
```

Пробросим порты сервиса и проверим его адрес:
```
minikube kubectl -- port-forward service/frontend 3300:3000
```

```
Forwarding from 127.0.0.1:3300 -> 3000
Forwarding from [::1]:3300 -> 3000
```

```
kubectl get services frontend
```

```
NAME       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
frontend   NodePort   10.97.255.174   <none>        3000:31225/TCP   3m

```

Заходим в веб интерфейс

![[Pasted image 20241102142722.png]]

По результатам  выполнения была получена следующая схема:
![[Lab2.png]]

# Выводы

По результатам выполнения лабораторной работы были написаны два манифеста, deployment.yml и service.yml описывающие соответствующие компоненты инфраструктуры. В манифесте deployment.yml были указаны переменные окружения передаваемые в приложение, а так же количество создаваемых реплик. Для унификации подов им автоматически был присвоен уникальный префикс.
