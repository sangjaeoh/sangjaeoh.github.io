---
layout: single
title: "kubernetes 기본사용법"
categories: [Kubernetes]
tag: [Kubernetes, k8s]
author_profile: true
toc: true
toc_sticky: true
---

💾 kubernetes의 기본 사용법을 정리한 내용입니다. 자세한 내용은 [링크](https://kubernetes.io/ko/docs/concepts/workloads/){:target="_black"}에서 확인할 수 있습니다.

<br/>


# 🎯 Pod
쿠버네티스와 도커의 차이점은 **도커는 컨테이너를 만들지만, 쿠버네티스는 Pod를 만듭니다.**  
Pod는 한 개 또는 여러 개의 컨테이너를 포함합니다.


## Pod 생성
```bash
# 생성
kubectl run nginx-pod --image=nginx

# 확인
kubectl get pod

# 출력
NAME READY STATUS RESTARTS AGE
nginx-pod 1/1 Running 0 35s
```
{% capture notice %}
kubernetes v1.18 이상은 run명령어가 pod을 만들지만 v1.17 이하는 deployment를 만듭니다.
{% endcapture %}
<div class="notice--warning">{{ notice | markdownify }}</div>
<br/>



## Pod 제거
```bash
kubectl delete pod/nginx-pod
```
<br/>



## YAML으로 파드 생성
**YAML 파일 정의**
```yaml
# simple-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx-pod
spec:
  containers:
    - name: app
      image: nginx
```
{% capture notice %}
**필수 요소**

| 정의 | 설명 | 예 |
| --- | --- | --- |
| version | 오브젝트 버전 | v1, app/v1, networking.k8s.io/v1, ... |
| kind | 종류 | Pod, ReplicaSet, Deployment, Service, ... |
| metadata | 메타데이터 | name과 label, annotation(주석)으로 구성 |
| spec | 상세명세 | 리소스 종류마다 다름 |

**`version, kind, metadata, spec` 는 리소스를 정의할 때 반드시 필요한 요소입니다.**
{% endcapture %}
<div class="notice--info">{{ notice | markdownify }}</div>


**Pod 생성**
```bash
kubectl apply -f simple-pod.yaml
```
<br/>




## 컨테이너 상태 모니터링
`컨테이너 생성`과 실제 `서비스 준비`는 약간의 차이가 있습니다. 서버를 실행하면 바로 접속할 수 없고 짧게는 수초, 길게는 수분의 초기화 시간이 필요한데 **실제로 접속이 가능할 때 서비스가 준비되었다고 말할 수 있습니다.**  
![컨테이너 준비](/assets/images/posts/kubernetes/20230327/532BF508-682A-46F6-8BF6-7C7852804169.png)


**livenessProbe**  
컨테이너가 정상적으로 동작하는지 체크하고 정상적으로 동작하지 않는다면 컨테이너를 재시작하여 문제를 해결합니다.
`정상`이라는 것은 여러가지 방식으로 체크할 수 있는데, 여기서는 `httpGet` 요청을 보내 확인하는 방법을 사용합니다.
{% capture notice %}
**상태체크**  
`httpGet` 외에 `tcpSocket`, `exec` 방법으로 체크할 수 있습니다.
{% endcapture %}
<div class="notice--info">{{ notice | markdownify }}</div>


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: echo-lp
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      livenessProbe:
        httpGet:
          path: /actuator/health
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2        # Default 1
        periodSeconds: 5         # Defaults 10
        failureThreshold: 1      # Defaults 3
```

정상적으로 응답하지 않는다면 Pod가 여러번 재시작되고, `CrashLoopBackOff` 상태로 변경됩니다.
```text
NAME      READY   STATUS             RESTARTS      AGE
echo-lp   0/1     CrashLoopBackOff   4 (15s ago)   60s
```
<br/>




**readinessProbe**  
컨테이너가 준비되었는지 체크하고 정상적으로 준비되지 않았다면 Pod으로 들어오는 요청을 제외합니다.
livenessProbed와 차이점은 문제가 있어도 Pod를 재시작하지 않고 요청만 제외한다는 점입니다.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: echo-rp
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      readinessProbe:
        httpGet:
          path: /actuator/health
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2               # Default 1
        periodSeconds: 5                # Defaults 10
        failureThreshold: 1             # Defaults 3
```
상태 확인
```text
NAME      READY   STATUS    RESTARTS   AGE
echo-rp   0/1     Running   0          14s
```
<br/>




**livenessProbe + readinessProbe**  
보통 `livenessProbe`와 `readinessProbe`를 같이 적용합니다. 상세한 설정은 애플리케이션 환경에 따라 적절하게 조정합니다.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: echo-health
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      livenessProbe:
        httpGet:
          path: /actuator/health
          port: 3000
      readinessProbe:
        httpGet:
          path: /actuator/health
          port: 3000
```
<br/>




**다중 컨테이너**  
대부분 `1 Pod = 1 컨테이너`지만 여러 개의 컨테이너를 가진 경우도 꽤 흔합니다. 
하나의 Pod에 속한 컨테이너는 서로 <span class="danger-color">네트워크를 localhost로 공유하고 동일한 디렉토리를 공유할 수 있습니다.</span>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
  labels:
    app: counter
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/counter:latest
      env:
        - name: REDIS_HOST
          value: "localhost"
    - name: db
      image: redis
```
{% capture notice %}
**환경변수 설정**  
`env`는 `name`과 `value`를 별도로 정의합니다.
{% endcapture %}
<div class="notice--info">{{ notice | markdownify }}</div>
<br/>
<br/>
<br/>







# 🎯 ReplicaSet
Pod을 단독으로 만들면 어떤 문제가 생겼을 때 자동으로 복구되지 않습니다. Pod을 정해진 수만큼 복제하고 관리하는 것이 ReplicaSet입니다.

## ReplicaSet YAML
```yaml
# echo-rs.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: echo-rs
spec:
  replicas: 1            # 원하는 pod 개수
  selector:              # lebel 체크 조건
    matchLabels:
      app: echo
      tier: app
  template:              # 생성할 pod의 명세
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
```
<br/>



## ReplicaSet 생성 및 확인

**생성**
```bash
# ReplicaSet 생성
kubectl apply -f echo-rs.yml
```

**확인**
```bash
# 리소스 확인
kubectl get po,rs

# 리소스 확인 결과
NAME                READY   STATUS    RESTARTS   AGE
pod/echo-rs-tcdwj   1/1     Running   0          61s

NAME                      DESIRED   CURRENT   READY   AGE
replicaset.apps/echo-rs   1         1         1       61s



# pod 라벨 확인
kubectl get pod --show-labels

# pod 라벨 확인 결과
NAME            READY   STATUS    RESTARTS   AGE   LABELS
echo-rs-tcdwj   1/1     Running   0          3m    app=echo,tier=app
```
ReplicaSet과 Pod이 같이 생성된 것을 볼 수 있습니다. <span class="danger-color">ReplicaSet은 label을 체크해서 원하는 수의 Pod이 없으면 새로운 Pod을 생성</span>합니다.  
<br/>



## 스케일 아웃
ReplicaSet을 이용하면 손쉽게 Pod을 여러개로 복제할 수 있습니다.

**replicas 변경**
```yaml
# echo-rs-scaled.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: echo-rs
spec:
  replicas: 4            # pod 개수 4개로 설정
  selector:
    matchLabels:
      app: echo
      tier: app
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
```

**실행 및 결과**
```bash
# 실행
kubectl apply -f echo-rs-scaled.yml

# 확인
kubectl get pod,rs

# 확인 결과
NAME                READY   STATUS    RESTARTS   AGE
pod/echo-rs-h4q86   1/1     Running   0          12m
pod/echo-rs-4wk6t   1/1     Running   0          2m12s
pod/echo-rs-nkgf2   1/1     Running   0          2m12s
pod/echo-rs-xpqnc   1/1     Running   0          2m12s

NAME                      DESIRED   CURRENT   READY   AGE
replicaset.apps/echo-rs   4         4         4       12m
```










<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

작성중....