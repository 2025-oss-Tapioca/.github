![Image](https://github.com/user-attachments/assets/18e52bb7-9358-4bc2-a22b-c1fb399d4066)

</br>

**Tapioca는 MCP를 활용한 웹, 앱 개발 보조 서비스입니다.** </br>
> Tapioca는 웹·앱 개발에 관심 있는 학생들이 빠른 초기 세팅과 안정적인 개발 환경을 갖출 수 있도록 시작되었습니다.</br>
> AI의 발전으로 코드 구현은 쉬워졌지만 여전히 복잡한 초기 설정과 개발 이후의 테스트·검증 단계를 단축하기 위해,.</br>
> MCP(Model Context Protocol)와  다양한 개발 도구들을 활용해 표준 보일러 플레이트 코드 템플릿, 성능·트래픽 테스트, </br>
> 로그 분석 CLI 등 다양한 기능들을 자동화 파이프라인으로 제공하여 개발,검증,배포,운영의 흐름을 일관되게 연결해줍니다. </br>

<br>

- MCP(Model Context Protocol) 기반 챗봇을 통해 사용자가 상황에 따라 다양한 툴들을 사용하도록 돕습니다.

- 표준 보일러플레이트 템플릿을 제공하여 폴더 구조, 코딩 규칙, 테스트 스켈레톤을 포함하고 초기 세팅 시간을 단축합니다.
  
- 성능·트래픽 테스트 체계를 마련하여 지연, 처리량, 성공률 등 핵심 지표를 표준화하고 병목을 조기에 발견해 재발을 방지합니다.
  
- 로그 분석 CLI를 통해 서비스, 기간, 레벨별 쿼리 탐색이 가능하며 문제 원인 파악과 롤백·재배포 같은 대응을 신속히 지원합니다.
  
- 자동 품질 게이트를 적용해 테스트, 린트, 보안 점검을 자동화하고 환경 간 일관성과 재현 가능성을 보장합니다.
  
- 이를 통해 개발 리드타임을 단축하고 품질과 안정성을 높이며 학습과 실무 현장에서 바로 활용할 수 있는 기반을 제공합니다.

</br>

## Architecture

![Image](https://github.com/user-attachments/assets/4e92fe39-d925-4242-833c-e3b1c13754bb)

본 프로젝트의 런타임은 웹 서비스·LangChain·MCP로 이루어진 3-서버 아키텍처를 채택했습니다. 웹 서비스와 MCP는 Spring Boot(Java)로 구현하여 안정적인 서버 사이드 생태계와 성숙한 라이브러리 기반을 활용하고, LLM 오케스트레이션과 프롬프트 체인을 담당하는 LangChain 서비스는 FastAPI(Python)로 구성해 경량·고성능의 비동기 처리를 확보했습니다. 세 서비스는 공용 영속 저장소로 PostgreSQL을 공유하여 하나의 일관된 트랜잭션 경계를 유지하며(서비스별 스키마 분리 권장), 대화형 기능의 지연을 최소화하기 위해 Redis(인메모리)를 함께 운용하여 사용자 대화 이력·세션 상태를 고속으로 저장·조회할 수 있도록 했습니다.

MCP 서버는 프로젝트의 도구 허브로서 개발·운영에 필요한 기능을 툴(TOOLS) 형태로 노출합니다. 우선 Vegeta를 연동해 API 성능·트래픽 테스트를 표준화하고, RPS/지속시간/헤더·바디 시나리오를 구성해 지연 분포(50/95/99th), 처리량, 성공률, 상태코드 등의 핵심 지표를 수집·보관합니다. 또한 GitHub MCP를 통해 브랜치/태그 중심의 배포 연계를 제공하고, 승인·보호 규칙 등 형상 관리 정책과 자연스럽게 맞물리도록 하였습니다. 더불어 PyGithub 라이브러리를 사용해 레포지토리 파일 생성, 트리(디렉터리) 구조 조회, Pull Request 생성 등을 자동화하는 툴을 구현하여 초기 템플릿 배치, 변경 제안(코드 모드), 운영 스크립트 업데이트 같은 반복 작업을 대화형으로 신속히 처리할 수 있게 했습니다. 무엇보다 이 아키텍처는 LangChain의 AI 엔진과 MCP 서버의 툴·도메인 로직을 인터페이스 기반으로 분리한 느슨한 결합 구조를 하여, 해당 모듈만 교체·확장하면 곧바로 다른 모델·프레임워크·툴셋으로 대체가 가능하므로 오픈소스 환경에서 손쉽게 재사용·기여·파생 개발을 할 수 있다는 장점을 가진 구조로 구성하였습니다.

</br>

## 🔑 Key Features


### 1. 성능/ 트래픽 테스트(vegeta)
<img width="1280" height="720" alt="Image" src="https://github.com/user-attachments/assets/5eef93f6-c98f-4159-84e2-b642c6dc0bcb" />
대상, 헤더/ 인증을 사용자가 입력한 ID, 비밀번호를 가지고 로그인 API를 호출해 Jwt를 자동으로 호출하며 진행합니다.</br>
입력에 따른 정보를 통해 시나리오를 구성하고 실행 한 후 지연 분포 50/95/99th, 처리량, 성공률, 상태코드등의 지표를 수집하고 저장합니다. </br></br>

### 2. 코드 스캐폴딩
<img width="1360" height="974" alt="Image" src="https://github.com/user-attachments/assets/e475d6cf-7292-44ea-8ace-6dd008f81170" />
사용자가 필요한한 테이블 정보를 입력하여 테이블을 생성하고, 테이블들의 관계를 연결하여 ERD를 제작할 수 있습니다.
<img width="1360" height="974" alt="Image" src="https://github.com/user-attachments/assets/ee85f6f8-79f5-4bc7-8326-8267f0e63750" /> 
제작한 ERD 기반으로 도메인 엔티티(JPA), 리포지토리(Spring Data), 서비스 레이어, 컨트롤러를 일괄 생성하고,
레이어드 패키징/기본 디렉토리 구조와 함께 DTO·매퍼·예외 처리·테스트 스켈레톤까지 자동 구성해 일관된 규약과 확장 가능한 토대를 제공합니다. </br></br>


### 3. 로그 분석 CLI 
<img width="1590" height="1128" alt="Image" src="https://github.com/user-attachments/assets/e8b6a6fd-5b91-4865-bd6b-8463568cf4e4" />
사용자 챗봇 요청에 따라 기간·서비스·레벨 등 필터를 지정해 조회·집계하며, 챗봇 화면에서는 요약 지표와 핵심 에러·빠른 필터를 즉시 확인하고, 
전체 대시보드에서는 전체 로그 스트림/검색/저장·내보내기와 에러 패턴·Trace/Span 상관관계를 상세히 탐색할 수 있습니다.</br></br>

## 📝 Tutorial
[![Image](https://github.com/user-attachments/assets/d121e2df-0be5-4237-bd6d-9fc0b847d491)](https://youtu.be/2SJrGJo5cbY?si=8E8WdqizejOiAjge)
</br>

## 👥 Team Information
| 이름                                       | 역할 | Email                |
| -------------------------------------------- | ------ | -------------------- |
| [정유현](https://github.com/top6543top) | PM/BE | top6543top@gmail.com |
| [공기훈](https://github.com/a1rhun) | FE | gongja124@dgu.ac.kr |
| [손승현](https://github.com/son0131) | FE | s209085@dgu.ac.kr |
| [윤찬영](https://github.com/cyyoon0311) | FE | cy_0311@naver.com|
| [이시우](https://github.com/sw4796) | AI | swlee4796@naver.com |
| [이재혁](https://github.com/Hyuk-II) | AI/BE | jaehyuk2.lee@gmail.com |
| [조수현](https://github.com/sssuhyeon) | BE | ckc020506@gmail.com |
| [황윤수](https://github.com/hys010730) | BE | githwang01@gmail.com |
</br>


## 🤝How to Contribute
Tapioca는 여러분의 소중한 기여를 기다리고 있습니다.  
궁금한 점이나 제안 사항은 언제든 아래 이메일로 문의해주세요.  

📬 **githwang01@gmail.com**

## ⚖️Licenses
이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [License](../LICENSE) 파일을 참고해주세요.
