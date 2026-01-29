# Sensor Agent Kafka Broker (Infrastructure)

## 📌 Overview

센서 에이전트의 인증 및 데이터 파이프라인 연결을 위해 **Kafka 리소스를 동적으로 프로비저닝**하는 인프라스트럭처 문서입니다.  
에이전트별 전용 계정(User), 토픽(Topic), 권한(ACL)을 자동으로 생성하고 관리합니다.

---

## 🏗️ Dynamic Provisioning Flow

에이전트 최초 인증 시, 다음과 같은 순서로 브로커 리소스가 설정됩니다.

1.  **Admin Connection**: 관리자 계정으로 Broker 접속 (SCRAM 등 보안 프로토콜 지원)
2.  **User Provisioning**: 에이전트 전용 계정 생성 
    *   Auth Method: `SCRAM-SHA-256`, `SCRAM-SHA-512` 지원
3.  **Topic Provisioning**: 요청/응답 채널 보장 
    *   `requestTopic`: 에이전트 → 서버 데이터 전송
    *   `responseTopic`: 서버 → 에이전트 명령 전달
4.  **ACL Configuration**: 최소 권한 원칙 적용 
    *   **Topic**: `READ`, `WRITE`, `DESCRIBE` 권한 부여
    *   **Consumer Group**: `READ`, `DESCRIBE` 권한 부여

---

## 🔒 Security & Policy (Infra Layer)

### 1. Authentication Strategy
브로커 접속 시 SASL 메커니즘을 사용하여 인증합니다.
- **Protocol**: `SASL_PLAINTEXT`, `SASL_SSL` 등 설정 가능
- **Mechanism**:
    - `SCRAM-SHA-256/512` (Main)
    - `PLAIN`, `OAUTHBEARER` (Optional)

### 2. Access Control List (ACL)
에이전트는 할당된 토픽과 그룹에 대해서만 접근이 허용됩니다.

| Resource Type | Resource Name | Operation | Permission |
|---|---|---|---|
| **Topic** | `requestTopic` | `READ`, `WRITE`, `DESCRIBE` | ALLOW |
| **Topic** | `responseTopic` | `READ`, `WRITE`, `DESCRIBE` | ALLOW |
| **Group** | `groupId` (Agent-Specific) | `READ`, `DESCRIBE` | ALLOW |

---

## 🔧 Implementation Details

### Broker Setup
AdminClient를 사용하여 리소스를 제어하는 핵심 로직입니다.

- **Idempotency**: 계정이나 토픽이 이미 존재할 경우, 에러를 발생시키지 않고 상태를 확인하거나 스킵합니다.
- **Safety**: `UnknownTopicOrPartitionException` 등을 핸들링하여 안정적인 프로비저닝을 보장합니다.

### Configuration Model (`KafkaBrokerConfig`)
```java
@Builder
public class KafkaBrokerConfig {
    private final String brokerUrl;
    private final Integer brokerPort;
    private final String requestTopic;
    private final String responseTopic;
    private final String authMethod;
    ....
}
```

---

## Usage Example (Backend)

```java
// 에이전트 엔티티로부터 설정 객체 생성
KafkaBrokerConfig config = KafkaBrokerConfig.fromEntity(agentEntity);

// 브로커 리소스 셋업 (User, Topic, ACL)
kafkaAdminService.setupBrokerResources(config);
```
