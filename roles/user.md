# 사용자(User) 역할

사용자는 코딩테스트 스터디 서비스의 기본 사용자 역할입니다. 스터디장이나 스터디원으로 참여하기 전의 상태입니다.

## 행위 및 도메인

```mermaid
flowchart LR
    subgraph Actions[유저 행위]
        direction TB
        SearchStudy((스터디 탐색))
        JoinStudy((스터디 참여))
    end

    %% 탐색 흐름
    SearchStudy -->|공고 조회| StudyAnnouncement1[스터디 공고]
    SearchStudy -->|스터디 조회| Study1[스터디]

    %% 참여 흐름
    JoinStudy -->|스터디 개설| Study2[스터디]
    Study2[스터디] -->|유저에게 스터디장 권한 부여| StudyRole[스터디 역할]

    JoinStudy -->|스터디 신청서 작성| StudyApplication1[스터디 신청서]
    StudyApplication1[스터디 신청서] -->|신청서 추가| StudyAnnouncement2[스터디 공고]
```

## 도메인별 기능

- Study (스터디)
    - 조회
    - 생성

- StudyAnnouncement (스터디 공고)
    - 조회

- StudyApplication (스터디 신청서)
    - 작성
    - 제출

- StudyRole (스터디 역할)
    - 역할 부여

## 역할 상태

```mermaid
stateDiagram-v2
    [*] --> User: 회원가입
    User --> Leader: 스터디 개설
    User --> Member: 스터디 가입 승인
```
