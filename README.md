# T.S.P Project (Spring Boot Backend)

이 프로젝트는 AI 언어 모델(번역, 요약, 의역) API를 하나의 웹 서비스로 통합한 **T.S.P Project의 백엔드 API 서버**입니다. Spring Boot를 기반으로 구축되었으며, React 프론트엔드와 **JWT(JSON Web Token) 기반으로 통신**하여 인증을 처리하고 사용자별 작업 히스토리를 관리합니다.

## 1. 주요 기능

* **JWT 기반 사용자 인증**: Spring Security와 JWT를 연동하여 Stateless 인증 시스템을 구현했습니다. 로그인 시 Access/Refresh Token을 발급합니다.
* **AI 기능 API 제공**:
    * 외부 AI API (OpenAI 등)를 호출하여 번역, 요약, 의역 기능을 수행하는 RESTful API 엔드포인트를 제공합니다.
    * **담당 역할**: 이 중 **외부 OpenAI API를 연동한 의역 기능 백엔드**를 직접 개발했습니다.
* **사용자 작업 히스토리**:
    * **담당 역할**: JWT 인증을 기반으로 **사용자별 작업 히스토리 기능 전체를 설계 및 구현**했습니다.
    * 사용자가 AI 기능을 사용할 때마다, Spring Security로 식별된 사용자와 작업 내역(입력/출력, 기능 종류)을 `Spring Data JPA`를 통해 `MariaDB`에 자동으로 저장합니다.
    * 사용자가 자신의 작업 히스토리 조회를 요청하면, 해당 사용자의 내역만 필터링하여 반환합니다.

## 2. 기술 스택

* **백엔드**: Spring Boot, Spring Security
* **데이터베이스**: MariaDB, Spring Data JPA
* **인증**: JWT (JSON Web Token)
* **HTTP 통신**: `REST Client` (혹은 `RestTemplate`/`WebClient`) (외부 AI API 호출용)
* **기타**: Tomcat

## 3. 아키텍처 흐름

1.  **인증**: React 클라이언트가 로그인 요청을 보내면, Spring Security가 인증을 처리하고 JWT(Access/Refresh Token)를 발급합니다.
2.  **API 요청**: React는 API 요청 시 `Authorization` 헤더에 Access Token을 첨부합니다. Spring Security의 JWT 필터가 토큰을 검증하고 사용자를 식별합니다.
3.  **기능 수행**: React가 '의역' API (`/api/paraphrase`)를 호출하면, 백엔드 컨트롤러는 `REST Client`를 사용해 외부 OpenAI API를 호출하고, 그 결과를 받아 React에 반환합니다.
4.  **히스토리 저장**: 이 과정에서 `Service` 계층은 `Spring Security`로 식별된 사용자의 ID와 작업 내역을 `JPA`를 통해 DB에 저장합니다.
5.  **히스토리 조회**: React가 '히스토리' API (`/api/history`)를 호출하면, `JPA`가 현재 사용자의 작업 내역만 조회하여 반환합니다.

## 4. 관련 리포지토리

* **프론트엔드 (React) 리포지토리**: [https://github.com/tlsdn741621/TranslationProject_Front](https://github.com/tlsdn741621/TranslationProject_Front)
