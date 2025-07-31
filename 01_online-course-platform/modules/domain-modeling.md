요구사항 
수강생(STUDENT)은:
-회원가입 / 로그인할 수 있어야 함
-강의를 수강할 수 있어야 함 (유료/무료)
-수강을 완료하면 수료증을 받을 수 있음

강사(INSTRUCTOR)는:
-강의를 등록할 수 있음

★도메인 모델링

1.요구사항 기준 User가 필요한 기능
* 로그인, 회원 가입 기능 
* 강의를 수강할 수 있는 기능 
* 수강을 마치면 수료증을 가지는 기능
=> id, password, name, email, role? // 너가 보내준 거 참조했음 

2.요구사항 기준 INSTRUCTOR가 필요한 기능
* 로그인, 회원 가입 기능
* 강의를 등록할 수 있는 기능
=> id, password, name, email,role? // 너가 보내준 거 참조했음 


3. 요구사항 기준 Course가 필요한 기능
* 강사가 강의를 등록하는 기능
=>id, password, name, price, lectureIntroduction, title

4. 요구사항 기준 Enrollment가 필요한 기능 
* 학생들이 강의를 조절하게 도와주는 도구 기능
=> stop, play, speedUp, speedDown, volumeUp, volumeDown

5. 요구사항 기준 Certificate가 필요한 기능 
* 수강을 완료한 학생에게 수강증을 발급하는 기능
=> courseStatus, courseComplet

도메인끼리의 관계 
User / Course 1:N 관계 => 어떤 한 유저가 여러 강의를 들을 수 있음 
Course / User 1:N 관계 => 한 강의를 여러 유저가 들을 수 있음 

Course / Enrollment N:1 => 여러 강의를 수강할 수 있음 
Certificate / Enrollment 1:1 => 한 개의 강의에는 한 개의 수료증만 발급됌 


======================================================================
# 📘 도메인 모델링 - Online Course Platform

## 1. User

- 역할: 학생(STUDENT), 강사(INSTRUCTOR)
- 주요 기능:
  - 회원가입 / 로그인
  - 강의 수강
  - 강의 등록 (강사인 경우)
- 필드:
  - id
  - name
  - email
  - password
  - role (STUDENT / INSTRUCTOR)
- 설명: 모든 핵심 행위의 주체이며, 권한 분리를 위해 `role`을 포함

---

## 2. Course

- 역할: 강사가 등록하는 강의
- 주요 기능:
  - 강의 등록 (INSTRUCTOR)
  - 강의 수강 대상
- 필드:
  - id
  - title
  - description
  - price
  - isFree (boolean) ← price == 0이면 true
  - instructorId (User id)
  - optional: instructorName (프론트에 표시용)
- 설명: 수강 대상이 되는 콘텐츠. 강사 정보는 userId로 참조하며, 이름은 UI에 표시 목적이면 따로 둘 수 있음

---

## 3. Enrollment

- 역할: 학생이 강의를 수강하고 있는 상태를 나타냄
- 주요 기능:
  - 수강 중인지 / 완료했는지 상태 추적
- 필드:
  - id
  - userId
  - courseId
  - status (IN_PROGRESS / COMPLETED)
  - completedAt
- 설명: 주체(User)와 대상(Course) 간의 관계를 추적하는 도메인. 수강 기록을 위해 반드시 필요

---

## 4. Certificate

- 역할: 수강 완료 후 발급되는 수료증
- 주요 기능:
  - 수강 완료 후 자동 또는 수동 발급
- 필드:
  - id
  - enrollmentId
  - issuedAt
- 설명: 수강 내역이 완료되었을 때만 생성되는 도메인. Enrollment에 종속됨 (1:1 관계)

---

## 🔗 도메인 간 관계

- `User 1 ─── * Enrollment`  
  (학생은 여러 강의를 수강할 수 있음)

- `Course 1 ─── * Enrollment`  
  (하나의 강의는 여러 수강생에게 수강됨)

- `Enrollment 1 ─── 0..1 Certificate`  
  (수료한 경우에만 수료증 발급)

---

## ✍️ 보충 설명

- `Course.password`는 설계 아이디어로서 흥미롭지만, 비즈니스적으로 강의 보호가 필요하다면 별도 보호 로직이 필요할 수 있음
- `Enrollment`에 UI 관련 기능은 도메인 객체가 아닌 별도 ViewModel/PlayerState 등으로 분리하는 것이 좋음
