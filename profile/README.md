<!-- ## Hi there 👋 -->

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
# 🦮 반쪽이
## 🙋 About
매년 끝없이 버려지는 유기견들을 위한 유기견 입양을 손쉽게 할 수 있도록 도와주는 커뮤니티 서비스입니다.

🐕 사용자는 보호소를 등록하고 강아지 입양글을 게시할수 있습니다.  
🐩 강아지를 입양하고자 하는 사용자는 입양글을 등록한 보호소와의 채팅을 통해 소통을 합니다.  
🐶 관심있는 강아지를 북마크하면 해당 강아지에 대한 알림을 받을 수 있습니다.  

1. **계정**
   - **회원가입**( `spring-security,` `Jwt filter` 를 사용하여 회원 관리)
     - 이메일 인증 또는 Google 인증을 통해 회원가입 수 있습니다.
       - OAuth 로그인
       - 일반 회원가입(이메일 인증 후 진행)
         - 회원가입 정보
           - 이메일(중복불가)
           - 비밀번호 (숫자, 영어 소문자, 영어 대문자, 특수기호 각 하나씩 포함하여 8자 이상)
           - 프로필사진 : `AWS S3` 에 저장하여 파일 url을 DB에 저장
           - 닉네임(10자 이내, 중복불가)
           - 자기소개(500자 이내)
       - 이메일 인증코드 발송
         - 이메일로 인증코드 전송 및 `Redis` 에 저장
         - 인증코드 만료시간 : 3분
         - 개인 정보는 암호화하여 저장
         - 권한 설정
           - 최초 가입자의 경우 `USER` 권한을 부여
           - 권한 종류
             - `USER` : 일반 사용자
             - `SHELTER` : 보호소 등록한 사용자
             - `ADMIN` : 관리자
   - **로그인**
     - 이메일 및 소셜(Google)로 로그인 할 수 있습니다.
       - 로그인 입력정보 : 이메일, 비밀번호
       - 이메일 로그인 성공 시 access token, refresh token 저장 및 반환
           - access token, refresh token `Redis` 저장
           - access token 만료시간 : 30분
           - refresh token 만료시간 : 1개월
   - **로그아웃**
     - 로그아웃 회원의 access token 을 `Redis` 에 저장(blacklist)
     - 로그아웃 회원의 refresh token 을 `Redis` 에서 삭제
   - **비밀번호 재설정**
     - 이메일 인증으로 가입한 경우 비밀번호를 재설정 할 수 있습니다.
     - 컨슈머에서 이벤트 확인, 메시지 수신자에게 `FCM` 전송
   - **탈퇴**
     - 서비스 이용을 탈퇴합니다.
       - 탈퇴 시 DB에 탈퇴여부만 입력하여 관리
2. **회원 프로필**
   - **사용자 정보**
       - 이메일, 프로필 이미지, 닉네임, 자기소개, 가입일 확인할 수 있습니다.
       - 관심 게시글 목록을 볼 수 있습니다.(북마크 기능)
       - 자신이 작성한 분양 게시글, 후기 게시글 목록을 볼 수 있습니다.
   - **사용자 정보 수정**
       - 본인만 수정할 수 있습니다.
       - `프로필 사진`, `닉네임`, `자기소개` 값을 받아 정보를 수정합니다.
3. **보호소**
   - **보호소 등록**
       - 사용자 중 보호소를 운영하고 있는 경우 별도 보호소 등록을 신청할 수 있습니다.
           - `보호소 이름`, `주소`, `설명`, `연락처`, `이미지`, `위도`, `경도` 입력하여 등록 신청
   - **보호소 승인**
       - 별도의 페이지 없이 관리자(`ADMIN` 권한)가 직접 승인
       - 승인 받은 사용자는 `SHELTER` 권한을 획득
       - 승인 받은 사용자 프로필 상단에 보호소 벳지가 표시
   - **보호소 정보 수정**
       - 해당 보호소만 수정할 수 있습니다.
       - 보호소 등록 시 입력한 데이터 중 수정할 사항이 있는 데이터를 입력받습니다.
   - **보호소 삭제**
       - 해당 보호소만 삭제 할 수 있습니다.
   - **팔로우 기능**
       - 일반 사용자는 보호소를 팔로우할 수 있습니다.
4. **분양 게시물(유기견 정보)**
   - **게시물 등록**
     - 승인된 사용자(보호소 등록 완료)만 유기견을 등록할 수 있습니다.
     - `제목`, `본문`, `강아지사진`, `견종`, `사이즈`, `건강검진 유무`, `중성화 유무`, `성별`, `나이`, `보호소 입소 일시` 값을 받아 등록합니다.
     - 사진을 최대 8장 등록합니다. (최대 8장)
     - 본문은 500자 이내로 작성합니다.
     - 제목은 50자 이내로 작성합니다.
   - **수정 / 삭제**
     - 수정 및 삭제는 등록한 사용자만 가능하며,
       종료된(분양완료) 게시글은 수정 및 삭제가 불가능합니다.
     - 수정은 `제목`, `본문`, `강아지사진`, `견종`, `사이즈`, `건강검진 유무`, `중성화 유무`, `성별`, `나이` 를 수정할 수 있습니다.
     - 사용자 간 상호작용(입양예약 & 입양완료) 으로 게시글의 상태가 변경될 수 있다.
       - `분양중`: 게시글을 등록하거나, 예약된 게시글이 10일 이내로 분양완료가 되지 않을때
       - `예약중`: 게시글 작성자가 예약중 입양예약자와 대화 후 `예약중`으로 상태 변경
       - `분양완료`: 게시글 작성자가 분양 완료 후 분양완료 상태로 변경
   - **게시물 목록 조회 & 검색**
     - 로그인을 하지 않은 사용자도 조회할 수 있습니다.
     - **`사진`, `제목`, `등록자`** 가 보여집니다.
     - `ElasticSearch` 을 이용해 키워드를 통해 필터링합니다.
       - **견종**
         - 텍스트 입력후 미리 저장된 태그 견종 선택
         - 20개 견종 중에 3개까지 선택
       - **사이즈**
         - 초소, 소, 중, 대 중 하나 선택 가능
       - **건강검진유무**
       - **중성화 유무**
       - **성별**
       - **나이 (나이 범위 설정)**
     - 키워드 설정이 없을땐 전체 데이터를 조회합니다.
     - 등록순으로 조회합니다.(최신순/오래된순 변경기능)
     - `무한 스크롤` 로 게시물이 보여집니다.
   - **상세내용 (게시물) 조회**
     - 게시글 등록 `유저`, 게시글 `제목`, `본문`, `강아지사진`, `견종`, `사이즈`, `건강검진 유무`, `중성화 유무`, `성별`, `나이`, `등록시간`을 조회합니다. (`마지막 수정 시간`)
       - `DayJS` 를 사용하여 등록시간을 “n시간전” 표현으로 구현
     - 분양중/ 예약중/ 분양완료 상태가 표시됩니다.
     - 게시물을 최대 20개까지 관심 등록할 수 있습니다.
     - 후기 게시물이 등록되었다면 하단에 표시됩니다.
5. **후기 게시물**
   - 후기 게시물 등록
     - 후기 게시물은 분양 게시글 당 1개만 등록할 수 있습니다.
     - `제목`, `본문`, `강아지사진` 값을 받아 등록합니다.
     - 사진을 최대 8장 등록합니다. (최대 8장)
     - 본문은 500자 이내로 작성합니다.
     - 제목은 50자 이내로 작성합니다.
   - **후기 게시물 목록 조회**
     - `ElasticSearch` 를 활용하여 등록순으로 후기 게시물 조회
   - **후기게시물 상세내용 조회**
     - `작성자`, `후기 게시글`, `제목`, `본문`, `강아지사진` 을 조회합니다.
6. **채팅**
   - **채팅방 생성 및 메세지 전송**
     - 입양할 사용자는 입양글을 대상으로 보호소와의 채팅을 만들 수 있습니다.
     - 채팅방의 유저는 최대 2명입니다.
     - 채팅방을 만들면서 소켓 연결, 채널 구독을 진행합니다.
     - 채팅을 통해 실시간 소통할 수 있습니다.
       - `STOMP` 를 이용해 WebSocket CONNECT, SUBSCRIBE, SEND 기능을 구현합니다.
       - `RabbitMQ`를 외부 브로커로 적용합니다.
     - `StompJS` 를 이용해 소켓 연결, 채널 구독을 구현합니다.
   - **채팅방 삭제(나가기)**
     - 본인이 속한 채팅방만 나갈 수 있습니다.
     - 한명의 사용자가 채팅방을 나가면 상대방에게 나갔다는 메세지를 전송합니다.
     - 두명의 사용자가 채팅방을 나가기 전에는 채팅방이 유지됩니다.
     - 두명의 사용자가 모두 나가면 채팅방이 delete 처리 됩니다.
   - **채팅방 리스트 조회**
     - 본인이 속한 채팅방 리스트를 볼 수 있습니다.
     - 마지막 채팅이 올라온 순서대로 정렬되어 보여집니다.
   - **채팅 메세지 리스트 조회**
     - 채팅 메세지 리스트를 `무한 스크롤` 로 구현했습니다.
7. **알림 설정**
   - **채팅 알림**
     - 클라이언트에서 메시지 수신, 이벤트 발생(`RabbitMQ`)
     - 컨슈머에서 이벤트 확인, 메시지 수신자에게 `FCM` 전송
   - **새로운 게시물 알림**
     - 분양에 대한 게시물을 올리면 해당 보호소를 팔로워를 한 사용자들이 알림을 받습니다.  (`FCM`)
     - 견종,사이즈,건강검진유무,중성화유무,나이(range),성별,지역 선택적 알림
   - **후기 게시물 등록 알림**
     - 후기 게시물의 원래 게시글을 작성한 유저(보호소)에게 알림을 보냅니다.
   - **관심 등록한 사용자는 해당 유기견의 분양 상태가 변경되면 알림을 받습니다.**
   - 알림창을 선택하면 새 게시물 알림이 가르키는 게시물로 이동합니다.
   - 알림창에서 새로운 알림 vs 읽었지만 삭제안한 알림 차이 표시
8. **지도 기능**
   - 등록된 보호소의 위치를 확인할 수 있습니다.

## 🧑‍🤝‍🧑 Members
| [BE 박태준](https://github.com/jnjeaaaat) | [BE 이정창](https://github.com/nadoran78) | [BE 이태희](https://github.com/eTaphee) | [BE 최성욱](https://github.com/Woogie95) | [FE 양희준](https://github.com/heejuny2) | [FE 유소민](https://github.com/SoMin-Yoo) |
|---|---|---|---|---|---|
|<img src="https://avatars.githubusercontent.com/u/47658862?s=400&v=4" width=200px alt="_"/>|<img src="https://avatars.githubusercontent.com/u/141199772?&v=4" width=200px alt="_"/>|<img src="https://avatars.githubusercontent.com/u/43163409?&v=4" width=200px alt="_"/>|<img src="https://avatars.githubusercontent.com/u/78896254?&v=4" width=200px alt="_"/>|<img src="https://avatars.githubusercontent.com/u/73383550?&v=4" width=200px alt="_"/>|<img src="https://avatars.githubusercontent.com/u/141366284?&v=4" width=200px alt="_"/>|


## 👻 Design
### Architecture
### API Spec
[API Spec](https://etapim.notion.site/API-9434d3215f564826b0dfb95219b758c8?pvs=4)

### ERD
![banzzokee ERD](https://github.com/banzzokee/.github/assets/47658862/ef6f635a-9757-4c60-86ca-d013464aba96)

###  Wireframe
## 🛠️ Skill Stack
### Frontend
### Backend
- Server
  - Java 17
  - Spring boot
  - Gradle
  - Spring Security
  - OAuth
  - JPA
  - Firebase Cloud Messaging(FCM)
  - STOMP
  - RabbitMQ
  - QueryDSL
  - JUnit5
  - Mockito
- DB
  - MySQL
  - ElasticSearch
  - Redis
- Infra
  - Docker
  - AWS ec2
  - AWS S3
  - AWS RDS
  - AWS ElastiCache
  - Github actions
