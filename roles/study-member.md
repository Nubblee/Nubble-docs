# 스터디원(Member) 역할

스터디원은 스터디에 참여한 유저로, 코딩테스트 문제를 풀고 다른 스터디원들과 상호작용하는 역할입니다.

## 행위 및 도메인

```mermaid
flowchart LR
    subgraph Actions[스터디원 행위]
        direction TB
        
        ViewSchedule((일정 조회))
        SearchProblems((문제 검색))
        WriteSolution((풀이 등록))
        ViewStudies((스터디 조회))
    end

    %% 스터디 조회 흐름
    ViewStudies -->|참여 스터디 목록|Study2[스터디]
    Study2 -->|멤버십 확인|StudyMembership3[스터디 멤버]

    %% 일정 조회 흐름
    ViewSchedule -->|1.참여 스터디 조회| ViewStudies1((스터디 조회))
    ViewSchedule -->|2.스터디별 일정 조회| Study1[스터디]
    Study1 -->|일정 조회|Schedule1[스터디 일정]

    %% 문제 검색 흐름
    SearchProblems -->|문제 검색 요청|Problem1[코딩테스트 문제]
    Problem1 --> |1.소속 스터디 확인|StudyMembership2[스터디 멤버]
    Problem1 -->|2.내 풀이 목록 조회|Solution1[문제 풀이]
    Problem1 -->|3.조건별 필터링|Problem1[코딩테스트 문제]

    %% 풀이 작성 흐름
    WriteSolution -->|템플릿 조회| Template1[풀이 템플릿]

    WriteSolution -->|풀이 작성|Solution2[문제 풀이]
    Solution2 -->|1.템플릿 형식 비교|Template2[풀이 템플릿]
    Solution2 -->|2.문제와 매칭|Problem2[코딩테스트 문제]
```

## 도메인별 기능

- 스터디 (Study)
  - 스터디원 관점에서는 별도 기능 없음
  - 다른 도메인과의 연관 관계를 위한 도메인으로 사용

- 스터디 멤버 (StudyMembership)
  - 소속 스터디 확인

- 스터디 일정 (Schedule)
  - 일정 조회

- 코딩테스트 문제 (Problem)
  - 문제-풀이 매칭 검증

- 풀이 템플릿 (Template)
  - 템플릿 조회
  - 템플릿 형식 검증

- 문제 풀이 (Solution)
  - 풀이 작성
  - 풀이 목록 조회

## 권한 및 제한사항
- 동시에 여러 스터디의 스터디원이 될 수 있음
- 스터디 진행 중에는 자유롭게 탈퇴 가능
- 본인이 등록한 풀이에 대해서만 수정/삭제 권한 보유

## 역할 상태

```mermaid
stateDiagram-v2
    [*] --> Member: 스터디 가입 승인
    Member --> [*]: 스터디 탈퇴
    Member --> Leader: 스터디장 권한 양도 받음
```

## 참고사항

멤버별 진행 상황, 스터디 전체 진행 상황과 같은 통계성 기능들은 도메인 모델링 단계에서 제외했습니다. 이러한 기능들은 `Problem`과 `Solution` 도메인의 데이터를 활용한 서비스 레벨의 기능으로 구현할 예정입니다.