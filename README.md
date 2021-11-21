# GraphQL
- [Youtube 얄팍한 코딩사전의 얄팍한 GraphQL & Apollo 강좌](https://www.youtube.com/watch?v=9BIXcXHsj0A)을 학습하면서 정리한 페이지
- [얄팍한 코딩사전의 얄팍한 GraphQL & Apollo 강좌 GitHub 페이지](https://www.yalco.kr/@graphql-apollo/1-1/) 참조
## 강의 준비물
- Node.js
  - 자바스크립트를 내 컴퓨터에서 실행할 수 있도록 해주는 환경
  - 다운로드 위치: https://nodejs.org/en/
- Visual Studio Code
  - 마이크로소프트에서 만든 소스코드 에디터
- 실습 코드
```bash
$git clone https://gitlab.com/yalco/yalco-inflearn-graphql-apollo.git
```
## GraphQL이 등장하기 전 - REST API란?
- 소프트웨어간 정보를 주고받는 방식
  - GraphQL 이전부터 사용
  - GraphQL과는 다른 방식 - 용도와 작업특성에 따라 적합한 것 사용
- 만약에 Client가 서버에 특정 동네의 인기업소에 대한 리스트에 대한 요청을 보내면, 서버는 아래와 같이 응답
```javascript
[
  {
    name: '30분짜장',
    category: 'chinese',
    tel: '##-####-####',
    rating: 4.6
  },
  {
    name: '피자파자마',
    category: 'italian',
    tel: '##-####-####',
    rating: 3.9
  },
  {
    name: '공중떡볶이',
    category: 'snack',
    tel: '##-####-####',
    rating: 4.9
  },
  ///...
]
```
- 데이터를 주고받을 주체들간 약속된 형식
  - URI 형식(어떤 정보를) X 요청 방식(어떻게 할 것인가)      

| 요청 형식  | 용도 |
| --------- | ----------- |
| GET       | 정보 받아오기|
| POST      | 정보 입력하기|  
| PUT/PATCH | 정보 수정하기|  
| DELETE    | 정보 삭제하기|
### 요청 보내기 실습
- 1-2-rest-api 프로젝트 실행
```bash
# nodemon 설치. 특정 프로젝트가 아닌 시스템에 설치
# 소스 코드가 바뀌면, 다시 실행해줌.
# 실행 시, node가 아니라, nodemon으로 실행하면 됨
npm install -g nodemon
#
# 프로젝트 모듈 설치
npm install
#
# 프로젝트 실행 명령어 (해당 프로젝트 폴더에서)
nodemon index.js
# 브라우저에서 localhost:3000 으로 확인
```
- VS Code의 Edit csv 확장: 데이터 수정에 용이
- Postman을 사용하여 정보 요청해 보기
- talend API tester를 이용하여 접속해 봄
#### 팀(들), 팀원 목록 받아오기
- GET 방식을 이용하여 데이터 가져 오기

| 요청 형식  | URI |
| --------- | ----------- |
| GET       | localhost:3000/api/team|
| GET       | localhost:3000/api/team/{id 번호}|  
| GET       | localhost:3000/api/people|  
| GET       | localhost:3000/api/people?{변수}={값}&{변수}={값} ...|
| GET       | localhost:3000/api/team/{id 번호}/people|
- 사용예 1: 개발자이면서 혈액형이 O형인 사람들의 목록을 받고 싶을 경우
```javascript
# GET 방식
http://localhost:3000/api/people?role=developer&blood_type=O
```
- 사용예 2: id가 1인 team의 사람들의 목록을 받고 싶은 경우
```javascript
# GET 방식
http://localhost:3000/api/team/1/people
```
#### 팀 추가 하기
- POST 방식을 이용하여 데이터 생성
| 요청 형식  | URI |
| --------- | ----------- |
| POST      | localhost:3000/api/team|
```javascript
# POST 방식
http://localhost:3000/api/team
Content-type:application/json
Req Body:
{
"manager": "Kim, KyuSham",
"office": "777A",
"extension_number": "#7750",
"mascot": "Tiger",
"cleaning_duty": "Monday",
"project": "Smart-Work"
}

Response Body:
{
"id": 6,
"manager": "Kim, KyuSham",
"office": "777A",
"extension_number": "#7750",
"mascot": "Tiger",
"cleaning_duty": "Monday",
"project": "Smart-Work"
}
```
#### 팀 수정 하기
- PUT 방식을 이용하여 데이터 수정
| 요청 형식  | URI |
| --------- | ----------- |
| PUT       | localhost:3000/api/team/{id 번호}|
```javascript
# PUT 방식
http://localhost:3000/api/team/6
Content-type:application/json
Req Body:
{
"manager": "David",
"office": "777A",
"extension_number": "#7750",
"mascot": "Lion",
"cleaning_duty": "Tuesday",
"project": "Smart-Work"
}

Response Body:
{
"manager": "David",
"office": "777A",
"extension_number": "#7750",
"mascot": "Lion",
"cleaning_duty": "Tuesday",
"project": "Smart-Work",
"id": "6"
}
```
#### 팀 삭제 하기
- DELETE 방식을 이용하여 데이터 수정
| 요청 형식  | URI |
| --------- | ----------- |
| DELETE    | localhost:3000/api/team/{id 번호}|
```javascript
# DELETE 방식
http://localhost:3000/api/team/6

Response Body:
[
    {
    "manager": "David",
    "office": "777A",
    "extension_number": "#7750",
    "mascot": "Lion",
    "cleaning_duty": "Tuesday",
    "project": "Smart-Work",
    "id": "6"
    }
]
```
### REST API의 한계
#### Case 1. 각 팀의 메니저와 오피스 호수만 필요할 때
- Overfectching 이슈
  - 딱 필요한 정보들만 받아올 수는 없을까?
  - 아래의 JSON Object처럼, 팀의 ``manager``과 ``office``정보만 추출하고 싶다면?
```javascript
[
  {
    "manager": "Mandy Warren",
    "office": "101A",
  },
  {
    "manager": "Stewart Grant",
    "office": "101B",
  },
  {
    "manager": "Smantha Wheatly",
    "office": "102A",
  },
  // ...
]
```
#### Case 2. 특정 팀의 매니저와 팀원들 명단이 필요할 때
- Underfectching 이슈
  - 내가 원하는 정보가 여러 계층에 걸쳐져 있는 경우
  - 필요한 정보들을 한번의 요청으로 받아올 수 없을까?
  - REST API의 경우, 팀의 정보를 받아오고 난 후, 다시 사람들의 정보를 다시 받아와야 함
```javascript
{
  "manager": "Mandy Warren",
  "members": [
    {
      "first_name": "Nathan",
      "last-name": "Jenkins"
    },
    {
      "first_name": "Isabella",
      "last-name": "Martin"
    },
    {
      "first_name": "Kate",
      "last_name": "Owen"
    },
    //...
  ]
}
```
## GraphQL로 정보 주고 받아보기
### 체험 프로젝트 실행
- 1-3-graphql-exp 프로젝트 실행
```bash
# package.json과 package-lock.json을 토대로 프로젝트 모듈 설치
$npm install
# 프로젝트 실행 명령어 (해당 프로젝트 폴더에서)
$nodemon index.js
Server ready at http://localhost:4000/
# 브라우저에서 localhost:4000 으로 확인
```
- Chrome 브라우저에서 ``localhost:4000``에 접속하면, apollo playground를 사용할 수 있음
- 사용예 1: 브라우저에서 팀들에 대한 모든 정보 요청
```bash
# Write your query or mutation here
query {
  teams {
    id
    manager
    office
    extension_number
    mascot
    cleaning_duty
    project
  }
}
```
- 사용예 2: 브라우저에서 팀들에 대한 ``manager``와 ``office`` 정보 요청
  - **REST API의 Overfectching 이슈가 해결**
```bash
# Write your query or mutation here
query {
  teams {
    manager
    office
  }
}
```
- 사용예 3: 특정 팀의 정보만 요청
```bash
# Write your query or mutation here
query {
  team(id:2) {
    id
    manager
    extension_number
    mascot
  }
}
```
- 사용예 4: 팀 정보와 해당 팀 멤버들의 정보들 받아오기
```bash
# Write your query or mutation here
query {
  team(id: 1) {
    manager
    office
    members {
      first_name
      last_name
    }
  }
}
```
- 사용예 5: 팀 목록과 역할 목록 받아오기
  - **Underfectching 이슈 해결**
```bash
# Write your query or mutation here
query {
  teams {
    manager
    office
    mascot
  }
  roles {
    id
    requirement
  }
}
```
- 사용예 6: 새 팀 추가
  - mutation 사용
```bash
mutation {
  postTeam (input: {
    manager: "John Smith"
    office: "104B"
    extension_number: "#9982"
    mascot: "Dragon"
    cleaning_duty: "Monday"
    project: "Lordaeron"
  }) {
    manager
    office
    extension_number
    mascot
    cleaning_duty
    project
  }
}
```
- 사용예 7: 특정 번호의 팀 정보 수정
  - mutation 사용
```bash
mutation {
  editTeam(id: 2, input: {
    manager: "Maruchi Han"
    office: "105A"
    extension_number: "2315"
    mascot: "Direwolf"
    cleaning_duty: "Wednesday"
    project: "Haemosu"
  }) {
    id,
    manager,
    office,
    extension_number,
    mascot,
    cleaning_duty,
    project
  }
}
```
- 사용예 8: 특정 번호의 팀 삭제
  - mutation 사용
```bash
mutation {
  deleteTeam(id: 3) {
    id,
    manager,
    office,
    extension_number,
    mascot,
    cleaning_duty,
    project
  }
}
```  
### GraphQL의 특징과 강점
- 필요한 정보들만 선택하여 받아올 수 있음
  - Overfectching 문제 해결
  - 데이터 전송량 감소
- 여러 계층의 정보들을 한 번에 받아올 수 있음
  - Underfetching 문제 해결
  - 요청 횟수 감소  
- 하나의 endpoint에서 모든 요청을 처리
  - **하나의 URI에 POST로 모든 요청 가능**

![graphql_post](./images/graphql_post.png)
## Apollo의 역할
- GraphQL로 서비스를 만들려면?
  - **GraphQL은 명세, 형식일 뿐**
- GraphQL 구현한 프레임워크의 역할과 종류
  - 백엔드에서 정보를 제공 및 처리
  - 프런트엔드에서 요청 전송
  - 백엔드 프레임워크: Apollo Server, [GrapQL.js](https://www.npmjs.com/package/graphql), [GraphQL Yoga](https://github.com/dotansimha/graphql-yoga)
  - 프런트엔드 프레임워크: Apollo Client, [AWS Amplify](https://docs.amplify.aws/), [Relay](https://relay.dev/)...
  - [프레임워크 전체 살펴보기- https://graphql.org/code/](https://graphql.org/code/)
- Apollo GraphQL을 사용한 이유
  - [사이트-https://www.apollographql.com/](https://www.apollographql.com/)
  - **백엔드와 프런트엔드 모두 제공**
  - 간편하고 쉬운 설정
  - 풍성한 기능을 제공
  - Apollo Client로 React, Vue, angular 모두 제공
  - 해당 사이트에서 Apollo Turitorial을 읽으면 도움
- 이 강의에서 만들어 볼 것
  - Apollo Server를 활용한 백엔드 서버 제작
    - [사이트-https://www.apollographql.com/docs/apollo-server/](https://www.apollographql.com/docs/apollo-server/)
  - Apollo Client와 React를 활용한 프런트엔드 웹 제작
    - [사이트-https://www.apollographql.com/docs/react/](https://www.apollographql.com/docs/react/)  
### GraphQL로 정보를 주고받는 방법
## Apollo를 사용한 GraphQL 프로그래밍 실습
### Node.js 기반 프로젝트
### 백엔드 서버 만들기
### 프론트엔드 웹사이트 만들기