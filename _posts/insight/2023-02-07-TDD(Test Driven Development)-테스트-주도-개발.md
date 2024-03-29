---
layout: single
title: "TDD(Test Driven Development) - 테스트 주도 개발"
categories: [Insight]
tag: [Insight, Test, TDD]
author_profile: true
toc: true
toc_sticky: true
image_directory: "tdd"
---

<br/>

# 🤔 TDD(Test Driven Development)란?
- TDD란 Test Driven Development의 약자로 테스트 주도 개발이라 합니다.
- TDD는 소프트웨어 개발 방법론 중의 하나로 **자동화된 테스트 코드를 작성한 후, 테스트를 통과하기 위한 코드를 개선하는 방식의 개발 방식**을 말합니다.   
<br/>
<br/>


# ⛹️‍♀️ TDD 개발 방법
![TDD lifecycle](/assets/images/posts/insight/tdd/9d2d7f30-25c2-4ef7-ae08-e67eb8cf2bb5.png)

1. 테스트 작성, 기능이 아직 구현되지 않았기 때문에 테스트가 실패한다.
2. 테스트를 성공하도록 프로덕션 코드 작성
3. 프로덕션 코드와 테스트 코드를 리팩토링  

위 3단계를 반복하며, 더 간단하고 개선된 디자인 패턴과 높은 품질의 코드를 작성하게 됩니다.
<br/>
<br/>


# 🎯 TDD 원칙
1. 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드(production code)를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
<br/>
<br/>



# 🎯 TDD의 효과

1. **빠르게 피드백 받을 수 있다.**  
TDD를 통해 시스템 구현(*"시스템이 동작하는가?"*)과 설계의 품질(*"시스템이 잘 구조화 되있는가?"*)에 관한 피드백을 받을 수 있습니다.  

2. **디버깅 시간을 단축 할 수 있다.**  
TDD의 경우 자동화 된 유닛테스팅을 전재하므로 특정 버그를 손 쉽게 찾아낼 수 있습니다.

3. **객체 지향적인 코드가 된다.**  
TDD를 사용하면 자연스럽게 단순한 단위로 작업을 진행하게 됩니다. 그러다 보면 기능별로 모듈화가 이루어집니다. 즉 강렬한 디자인 사고 때문이 아니라 상호 연결된 많은 작은 모듈의 상호 작용으로 복잡한 기능을 처리하기 때문에 더 나은 코드를 갖게 됩니다.

4. **프로그래머의 오버 엔지니어링을 방지한다.**  
프로그래머들은 간혹 계획하지 않았던 코드를 추가하여 오버 엔지니어링하는 경우가 있습니다. 하지만 TDD의 원칙 중 하나는, 테스트를 통과하기 위한 최소한의 코드만 작성 및 개선해야 한다는 것입니다. 기능 단위로 테스트를 진행하기 때문에, 문제가 발견되지 않은 코드에 영향을 줄 수 있는 오버 코딩은 하지 않습니다.

5. **문서의 역할을 한다.**  
TDD를 사용하면, 테스트 코드를 작성하는 과정에서 히스토리가 남습니다. TDD를 통해 작성한 테스트 코드를 트래킹하면서 과거에 어떤 인과관계로 의사결정을 했는지 확인하기 쉽습니다.

6. **유지보수(리팩토링)이 용이하다.**  
TDD의 경우 단위 테스트 기반의 테스트 코드를 작성하기 때문에 추후 문제가 발생하였을 때 각각의 모듈별로 테스트를 진행해보면 문제의 지점을 쉽게 찾을 수 있습니다.

7. **변화, 추가 구현이 용이하다.**  
개발이 완료된 소프트웨어에 어떤 기능을 추가할 때 가장 우려되는 점은 해당 기능이 기존 코드에 어떤 영향을 미칠지 알지 못한다는 것입니다. 하지만 TDD의 경우 자동화된 유닛 테스팅을 전제하므로 테스트 기간을 획기적으로 단축시킬 수 있습니다.  
<br/>
<br/>



# 😓 TDD의 한계
이론적으로, 그리고 실제로 많은 경우에 TDD는 소프트웨어 개발에 많은 장점이 있습니다. 그러나 아래와 같은 한계도 존재합니다.

1. **레거시 코드**  
코드가 이미 작성되었기 때문에 레거시 코드에 적용되지 않습니다.

2. **생산성의 저하**  
장기적으로 보면 생산성이 증가합니다. 하지만 이는 **"급하지 않은 환경"**에서의 경우입니다. 납기일 준수가 중요한 SI프로젝트 같은 경우 이러한 조건과 모순됩니다. 

3. **불완전한 테스트 사례**  
프로그래머가 테스트 시나리오를 누락한다면, TDD는 사용자를 보호하지 않습니다.  
<br/>
<br/>



# 🤔 TDD 필요한가?
점점 더 많은 개발자들이 TDD로 전환하고 있습니다. 하지만 일부는 TDD가 실제 코딩 대신 테스트를 작성하는 데 시간을 더 소비한다고 주장합니다. 

빠르게 코딩하고 빠르게 제품을 만들어야 하는 스타트업 개발자와 같이 시간이 제한된 개발자에게는 이러한 시간 소비가 치명적일 수 있습니다. 또한 관리자와 고객의 TDD의 장점에 대한 이해 부족과 개발자 교육의 투자 비용은 TDD를 사용하지 않게 합니다.

하지만 리소스를 보유하고 있으며 코드 품질이 가장 중요한 기업은 TDD로 전환을 하는 것이 좋습니다.
<br/>
<br/>