# 🛡️ PR Guard

**AI로 작성된 코드가 대충 머지되는 것을 막는 PR 리뷰 파이프라인**

동국대학교 창의융합경진대회 2026

---

## 왜 만드나

AI 코딩 도구로 만든 코드는 그럴듯해 보이지만, 요청하지 않은 변경·미완성 구현·기존 코드와의 충돌·보안 규칙 누락이 자주 섞여 들어옵니다.
코드 생성 과정에는 끼어들 수 없으니, **Pull Request가 올라오는 시점**에서 막습니다.

diff만 LLM에 넣는 일반 AI 리뷰와 달리, PR Guard는 **diff + 레포 전체 + git 이력 + PR 설명**을 함께 분석하고, 근거를 붙여 리뷰합니다.

## 동작 방식

```
public 레포 URL 등록
        ↓
PR 자동 감지
        ↓
레포 인덱스 (심볼 · 호출 그래프 · 컴파일 체크 · git 통계)
        ↓
 A. 의도 대비 검증   B. 변경 영향 분석   C. 보안 일관성   D. 변경 위험도
        ↓
판정 → PR 리뷰 코멘트
```

| 검사 | 하는 일 |
|---|---|
| **A. 의도 대비 검증** | PR 설명·커밋의 의도와 실제 변경을 대조해 요청하지 않은 변경, 빠진 구현을 찾음 |
| **B. 변경 영향 분석** | 바뀐 코드를 호출하는 곳을 거슬러 올라가 깨지는 지점과 데이터 정합성 문제를 찾음 |
| **C. 보안 일관성** | 새 API를 비슷한 기존 API와 비교해 권한 검사 누락 등을 찾음 |
| **D. 변경 위험도** | git 이력상 늘 함께 바뀌던 파일이 이번에 빠졌는지 확인 |

- 근거는 정적 분석·그래프·통계로 만들고, LLM은 해석만 맡습니다.
- 이번 PR이 새로 만든 문제만 보고합니다.

> 현재는 레포 등록 → PR 감지 → AI 리뷰 → PR 코멘트까지 동작하며, A~D 검사를 순서대로 붙이는 중입니다.

## 레포지토리

| 레포 | 설명 |
|---|---|
| [pr-guard-api](https://github.com/dongguk-creative-fusion-2026/pr-guard-api) | 백엔드 — PR 폴링, 분석 파이프라인, 리뷰 코멘트 (Spring Boot) |
| [pr-guard-web](https://github.com/dongguk-creative-fusion-2026/pr-guard-web) | 프론트엔드 — 레포 등록, 리뷰 결과 화면 (Next.js) |
| [pr-guard-sandbox](https://github.com/dongguk-creative-fusion-2026/pr-guard-sandbox) | 테스트·시연용 레포 |

## 바로 보기

- 서비스: https://pr-guard-web.vercel.app
- 리뷰 예시: [pr-guard-sandbox PR #1](https://github.com/dongguk-creative-fusion-2026/pr-guard-sandbox/pull/1)
