---
title: 오라클 프로시저(Procedure) 생성 및 실행
author: Banjung
date: 2022-03-25 10:00:00 +0800
categories: [PL/SQL, Procedure]
tags: [PL/SQL]
math: true
mermaid: true
---

## 프로시저란?
- 자주 사용하는 SQL을 프로시저로 만든 뒤, 필요할 때마다 호출합니다.
- 작업의 효율성을 늘릴 수 있습니다.
- 함수(Function)와의 차이점
  - 함수(Function): 특정 연산을 수행한 뒤, 결과 값을 반환한다.
  - 프로시저(Procedure): 특정 로직을 처리하고 결과 값을 반환하지 않습니다.
## 프로시저 생성

```
CREATE OR REPLACE PROCEDURE TEST_PROC
(
    P_MASK_ID IN VARCHAR2,
    P_POD_ID IN VARCHAR2,
    P_DEFECT_COUNT IN NUMBER
)

BEGIN
INSERT INTO INSPECTION (MASK_ID, POD_ID, DEFECT_COUNT)
VALUES (P_MASK_ID, P_POD_ID, P_DEFECT_COUNT);
COMMIT;

END TEST_PROC
`````````

## 프로시저 실행

```
EXEC TEST_PROC('S10G540A', 'RDE', 80);
```

## 프로시저 결과

|MASK_ID|POD_ID|DEFECT_COUNT|
|---------|---------|---------|
|S10G540A|RDE|80|