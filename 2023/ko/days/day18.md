# DAST (동적 애플리케이션 보안 테스트)
DAST(Dynamic Application Security Testing)는 외부 소스로부터의 공격을 시뮬레이션하여 애플리케이션의 보안을 평가하는 데 사용되는 기술입니다.
아이디어는 가능한 한 많은 블랙박스 침투 테스트를 자동화하는 것입니다. 
실제 사람의 시간을 절약하고 다른 보안 도구(예: IAST)로의 트래픽을 생성하는 데 추가로 사용할 수 있습니다.

그럼에도 불구하고 조직이 애플리케이션을 프로덕션 환경에 배포하기 전에 개발 프로세스 초기에 잠재적인 취약점을 발견할 수 있도록 도와주기 때문에 SSDLC의 필수 구성 요소입니다. DAST 테스트를 수행함으로써 조직은 보안 사고를 예방하고 공격자에 의해 데이터와 자산이 손상되지 않도록 보호할 수 있습니다.

## 도구

DAST를 수행하는 데 사용할 수 있는 다양한 오픈 소스 도구가 있습니다. ZAP, Burp Suite, Arachni 등이 있습니다.

 이러한 도구는 SQL 인젝션, 크로스 사이트 스크립팅 및 기타 일반적인 취약점 등 애플리케이션에 대한 다양한 유형의 공격을 시뮬레이션할 수 있습니다. 
 
 예를 들어 애플리케이션이 SQL 인젝션에 취약한 경우, DAST 툴은 애플리케이션에 `OR 1=1 --`과 같은 악성 SQL 쿼리를 전송하고 응답을 평가하여 취약한지 여부를 확인할 수 있습니다. 애플리케이션이 취약한 경우 데이터베이스의 모든 레코드를 반환하여 SQL 인젝션 공격이 성공했음을 나타낼 수 있습니다.

일부 테스트는 상당히 침략적일 수 있으며(예: 'DROP TABLE' 또는 이와 유사한 것을 포함할 수 있음), 적어도 데이터베이스에 많은 양의 테스트 데이터를 넣거나 심지어 앱에 DOS를 가할 수도 있습니다.

> __DAST 도구는 프로덕션 환경에서 절대 실행해서는 안 됩니다!!!__

모든 도구에는 애플리케이션에 대한 인증 가능성이 있으며 이로 인해 프로덕션 자격 증명이 손상될 수 있습니다. 또한 테스트 환경에 대해 인증된 스캔을 실행할 때는 적절한 역할을 사용해야 합니다(물론 RBAC 모델이 존재하는 경우 애플리케이션에 대해). 예를 들어, DAST는 다른 사용자를 삭제하거나 수정할 수 있는 역할을 사용하면 전체 환경을 사용할 수 없게 될 수 있으므로 사용하지 않아야 합니다.
다른 테스트 방법론과 마찬가지로 범위를 분석해야 하므로 불필요한 대상을 검사하지 않습니다.

## 사용법
일반적인 오류는 실제 애플리케이션이 아닌 보상 보안 제어 (compensating security control, 예: WAF)를 스캔하는 것입니다. 

DAST는 본질적으로 애플리케이션 보안 테스트 도구이며 보안 완화가 아닌 실제 애플리케이션에 대해 사용해야 합니다. 상당히 표준화된 공격을 사용하기 때문에 외부 제어가 공격 트래픽을 차단할 수 있으며, 이러한 방식으로 잠재적으로 악용될 수 있는 플로우를 커버할 수 있습니다.(정의에 따라 공격자는 결국 이러한 조치를 우회할 수 있음)

실제 스캔은 매우 느리기 때문에 때때로 DevOps 파이프라인 외부에서 실행해야 합니다. 야간이나 주말에 실행하는 것이 좋은 예입니다. 일부 간단한 도구(zap / arachny 등)를 파이프라인에 사용할 수 있지만 스캔의 특성으로 인해 전체 개발 프로세스의 속도가 느려질 수 있습니다.

DAST 테스트가 완료되면 결과를 분석하여 발견된 취약점을 식별합니다. 그런 다음 조직은 취약점을 해결하고 애플리케이션의 전반적인 보안을 개선하기 위해 적절한 수정 조치를 취할 수 있습니다. 여기에는 기본 코드 수정, 입력 유효성 검사 및 필터링과 같은 추가 보안 제어 구현 또는 두 가지 모두 포함될 수 있습니다.

결론적으로, 애플리케이션의 보안을 보장하기 위해서는 SSDLC에서 DAST를 사용하는 것이 필수적입니다. 개발 프로세스 초기에 DAST 테스트를 수행하고 취약점을 식별함으로써 조직은 보안 사고를 예방하고 잠재적인 위협으로부터 자산을 보호할 수 있습니다. ZAP, Burp Suite, Arachni와 같은 오픈 소스 도구를 사용하여 DAST 테스트를 수행하고 조직의 전반적인 보안 태세를 개선할 수 있습니다.
DevSecOps 파이프라인의 다른 모든 도구와 마찬가지로 DAST가 유일한 스캐너가 되어서는 안 되며, 다른 모든 도구와 마찬가지로 모의 침투 테스트와 모범 개발 관행을 대체할 수 없습니다.

## 유용한 링크와 오픈소스 도구가 몇 가지
- https://github.com/zaproxy/zaproxy
- https://www.arachni-scanner.com/
- https://owasp.org/www-project-devsecops-guideline/latest/02b-Dynamic-Application-Security-Testing 

[19일차](day19.md)에 뵙겠습니다.
