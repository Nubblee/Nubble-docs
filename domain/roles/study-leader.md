# 스터디장 역할

스터디장은 스터디를 개설하고 운영하는 역할입니다. 스터디의 관리와 운영에 대한 권한을 보유하고 있습니다.

## 행위 및 도메인

```mermaid
flowchart LR
    subgraph Actions[스터디장 행위]
        direction TB
        CreateAnnouncement((공고 작성))
        RecruitMembers((스터디원 모집))
        ManageStudy((스터디 관리))
        ManageProblems((문제 관리))
        ManageNotices((공지사항 관리))
    end

    %% 공고 작성 흐름
    CreateAnnouncement -->|신청서 양식 작성| ApplicationForm1[신청서 양식]
    CreateAnnouncement -->|공고 작성| StudyAnnouncement1[스터디 공고] --> |신청서 양식 연결| ApplicationForm2[신청서 양식]
    
    %% 모집 흐름
    RecruitMembers --> |신청서 검토| Application1[스터디 신청서]

    RecruitMembers --> |신청 수락| Application2[스터디 신청서]
    Application2[스터디 신청서] --> |공고 반영| StudyAnnouncement2[스터디 공고]
    StudyAnnouncement2[스터디 공고] --> |스터디원 추가| Study1[스터디]
    Study1 --> |멤버 등록| StudyMembership1[스터디 멤버]

    RecruitMembers --> |신청 거부| Application3[스터디 신청서]
    Application3[스터디 신청서] --> |거부 사유 작성| RejectionReason[거부 사유]

    %% 스터디 관리 흐름
    ManageStudy((스터디 관리)) --> |상태 변경| Study2[스터디]
    ManageStudy((스터디 관리)) --> |정보 변경| Study3[스터디]
    ManageStudy -->|멤버/역할 관리| StudyMembership2[스터디 멤버]

    %% 문제 관리 흐름
    ManageProblems -->|문제 등록| Problems1[코딩테스트 문제]

    %% 공지사항 관리 흐름
    ManageNotices -->|공지사항 작성| Notice1[공지사항]
    ManageNotices -->|공지사항 고정| Notice2[공지사항]
```

## 도메인별 기능

- 신청서 양식 (ApplicationForm)
    - 양식 작성

- 스터디 공고 (StudyAnnouncement)
    - 공고 작성
    - 신청서 양식과 연결

- 스터디 신청서 (Application)
    - 신청서 검토
    - 신청 수락
    - 신청 거부

- 스터디 (Study)
    - 기본 정보 관리
    - 진행 상태 관리

- 스터디 멤버 (StudyMembership)
    - 멤버 등록/삭제
    - 역할 관리

- 코딩테스트 문제 (Problems)
    - 문제 등록/수정/삭제
    - 제출 현황 관리

- 공지사항 (Notice)
    - 공지사항 작성/수정/삭제
    - 공지사항 고정/해제

## 권한 및 제한사항

- 스터디당 한 명의 스터디장만 존재 가능
- 스터디장은 모든 관리 기능에 대한 접근 권한 보유
- 스터디 개설자가 스터디장이 됨
- 스터디장은 탈퇴 불가 (스터디 상태와 무관)
    - 단, 다른 스터디원에게 권한 양도 후 탈퇴 가능
- 스터디장은 다른 스터디원에게 스터디장 권한 양도 가능
    - 양도 시 이전 스터디장은 스터디원이 됨
    - 새로운 스터디장은 모든 스터디장 권한을 승계

## 역할 상태

```mermaid
stateDiagram-v2
    [*] --> Leader: 스터디 개설
    Leader --> Member: 권한 양도
    Member --> Leader: 권한 양도 받음
```
