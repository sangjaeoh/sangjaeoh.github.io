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


## Pod 제거
```bash
kubectl delete pod/nginx-pod
```


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
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

작성중....