---
title: "데이터 모델링"
excerpt: "후기"

categories:
  - study
tags:
  - 데이터 모델링
  - Database
---

## 데이터 모델링

- 데이터 모델링의 절차
  1. 개념 모델링 
    - 주제영역도출
    - 주요 엔티티 도출 및 정의
    - 식별자 정의
    - 주요 엔티티 관계 정의
  2. 논리 모델링(검수 대상)
    - 전체 엔티티 도출
    - 식별자 정의
    - 전체 속성 도출
  3. 물리 모델링(검수 대상)
  4. DB 구축


- 모델 검수
  1. 표준화 관점
    - 데이터 표준화 : 명명규칙/표준 용어/표준 도메인이 데이터 모델에 반영 되었는지 여부
    - 표준단어사전 -> 표준용어사전 -> 표준코드 / 표준 도메인 사전
    - 데이터 문서화 : ERD, 테이블 정의서, 속성 정의서 등의 문서
    - 업무규칙 상세화 : 데이터 모델에 업무 규칙이 얼마나 반영되었는가
  
  2. 구조적 관점
    - 엔티티의 적절성
    - 식별자의 적절성
    - 관계의 적절성
    - 속성의 적절성

  3. 기능적(비즈니스) 관점
    - 데이터 활용 가능성

- 데이터 모델링 주의할점(TF별 피드백 종합)
  - 같지 않은 데이터는 다른 테이블이라도 같은 이름으로 사용하지 않는 것이 좋음 ex) id
  - 시스템 공통속성 관리를 권장함
    - 등록일시, 등록자, 수정일시, 수정자 관련 정보를 포함할 것을 권장
    - 컬럼순서는 테이블의 맨 뒤로 배치
  - 테이블 명은 확장성을 고려하여 네이밍하는 것이 좋음
  - 관계 표현을 명확하게 정하는 것이 좋음
  - 반정규화를 고려할 때 반정규화를 하면 업데이트를 여러번하게 되기 때문에 많이 수정되는 컬럼인지 고려가 필요
  - 논리명인 한글 테이블 명과 한글 컬럼 명은 영문 이름과 완전히 매칭되게 작성해야함
  - 1:1 관계인 테이블은 따로 테이블을 생성할 필요없음
  - 고정적으로 다량의 데이터를 저장해야 된다면 다량의 데이터를 어떤식으로 처리할지 고려해야함 ex)성능적 측면
  - 데이터베이스 네이밍 시 대문자, 소문자를 혼용하지 않음(사내에서는 대부분 소문자 사용)
  - 데이터베이스 테이블 Comment : 정의, 관리 범위, 데이터 생성 규칙 등
  - 예약이 한번에 몰리는 상황에서 테이블 업데이트가 많이 발생할 때 처리 방법
  - 데이터베이스 설계는 확장성을 어느정도 예상하고 설계하는 것이 좋음
  - 데이터 모델링 시 논리적인 부분은 최대한 반영하는 것이 좋음