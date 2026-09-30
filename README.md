# 단서 공유 교실 — 완전 새로 시작하는 버전

이 버전은 이전 Firebase 프로젝트나 이전 GitHub 저장소를 전혀 사용하지 않는 것을 기준으로 만들었습니다.

## 파일

- `index.html` : 학생용
- `teacher.html` : 교사용
- `firebase-config.js` : 새 Firebase 프로젝트의 웹 앱 설정
- `firestore.rules` : 새 Firestore에 적용할 보안 규칙
- `README.md` : 설치 방법

---

# 1. 새 Firebase 프로젝트 만들기

Firebase Console에서 새 프로젝트를 만듭니다.

프로젝트가 만들어지면:

1. 프로젝트 개요에서 `</>` 웹 앱 아이콘 클릭
2. 앱 이름 입력
3. Firebase Hosting은 체크하지 않아도 됨
4. 앱 등록
5. 화면에 나오는 `firebaseConfig` 값을 확인

`firebase-config.js`를 열고 새 프로젝트 값으로 바꿉니다.

예:

```js
export const firebaseConfig = {
  apiKey: "...",
  authDomain: "...firebaseapp.com",
  projectId: "...",
  storageBucket: "...firebasestorage.app",
  messagingSenderId: "...",
  appId: "..."
};
```

`measurementId`는 없어도 됩니다.

---

# 2. 새 Firestore Database 만들기

Firebase Console:

1. `Firestore Database`
2. `Create database`
3. 위치 선택
4. 시작 모드는 어느 쪽이어도 괜찮지만, 생성 후 아래 Rules로 반드시 교체

컬렉션이나 문서를 직접 만들 필요는 없습니다.

학생이 처음 단서를 올리면 자동으로 생성됩니다.

---

# 3. Authentication 설정 — 중요

이 사이트는 학생 개인정보를 받지 않기 위해 학생은 `Anonymous` 로그인을 사용합니다.

Firebase Console → Authentication → Sign-in method 에서:

## A. Anonymous

`Anonymous`를 찾아 `Enable`

학생용 사이트에 필요합니다.

## B. Email/Password

`Email/Password`를 `Enable`

교사용 사이트에 필요합니다.

---

# 4. 교사 계정 만들기

Firebase Console:

Authentication → Users → Add user

선생님이 사용할:

- 이메일
- 비밀번호

를 하나 만듭니다.

이 이메일은 학생 화면에 노출되지 않습니다.

---

# 5. Firestore Rules 적용

Firebase Console:

1. Firestore Database
2. Rules
3. 기존 내용을 전부 삭제
4. 이 폴더의 `firestore.rules` 내용을 전부 복사
5. 붙여넣기
6. `Publish`

이 단계가 매우 중요합니다.

---

# 6. 새 GitHub 저장소 만들기

GitHub에서 새 저장소를 만듭니다.

저장소 가장 바깥(root)에 아래 파일 4개를 올립니다.

- `index.html`
- `teacher.html`
- `firebase-config.js`
- `firestore.rules`

`README.md`도 같이 올려도 됩니다.

---

# 7. GitHub Pages 켜기

GitHub 저장소:

Settings → Pages

- Source: Deploy from a branch
- Branch: main
- Folder: /(root)

Save

잠시 기다리면 사이트 주소가 생성됩니다.

---

# 8. 학생 주소

예를 들어 GitHub Pages 주소가:

```text
https://myid.github.io/clue-classroom/
```

이라면 1반 학생 주소:

```text
https://myid.github.io/clue-classroom/?class=1
```

2반:

```text
https://myid.github.io/clue-classroom/?class=2
```

3반:

```text
https://myid.github.io/clue-classroom/?class=3
```

Firestore Database는 하나만 사용합니다.

반별 데이터는 자동으로:

```text
class-01
class-02
class-03
```

으로 분리됩니다.

---

# 9. 교사용 주소

1반:

```text
https://myid.github.io/clue-classroom/teacher.html?class=1
```

2반:

```text
https://myid.github.io/clue-classroom/teacher.html?class=2
```

교사용 페이지에서 Firebase Authentication에 만든 이메일/비밀번호로 로그인합니다.

---

# 10. 학생 활동 흐름

학생은 이름이나 학번을 입력하지 않습니다.

입력하는 것은:

1. 모둠 이름
2. 자기 단서

뿐입니다.

처음 접속하면 Firebase가 해당 브라우저에 익명 UID를 자동으로 부여합니다.

단서를 올리면:

- 같은 모둠 학생의 단서만 보임
- 다른 모둠 단서는 서버 규칙상 읽을 수 없음
- 자기 단서에만 `수정` 버튼이 나타남

이전 버전처럼 단순히 화면만 가리는 방식이 아닙니다.
Firestore Rules에서도 다른 모둠 읽기를 막습니다.

---

# 11. 전체 공개

교사용 페이지에서:

`모든 모둠 단서 공개`

를 누르면 Firebase의 공개 설정이 바뀝니다.

학생 화면은 실시간으로 바뀌며 다른 모둠 단서까지 보입니다.

다시:

`다른 모둠 단서 가리기`

를 누르면 다시 자기 모둠만 볼 수 있습니다.

---

# 12. 수업 초기화

교사용 페이지의:

`현재 반 데이터 전체 삭제`

를 누르면 해당 반의:

- clues
- members

데이터가 삭제되고 공개 상태가 잠금으로 돌아갑니다.

다른 반의 데이터에는 영향을 주지 않습니다.

---

# 13. 개인정보 관련

학생은 이름, 학번, 이메일, 전화번호를 입력하지 않습니다.

학생 구분은 Firebase Anonymous Authentication이 발급하는 임의 UID로 처리됩니다.

교사 계정은 선생님 본인이 Firebase Console에서 직접 만든 이메일/비밀번호만 사용합니다.

---

# 14. 테스트 순서 추천

처음부터 학생 20명으로 하지 말고 아래처럼 먼저 시험하세요.

1. PC 크롬 일반 창 → 학생 사이트 → `1모둠` + 단서 등록
2. PC 크롬 시크릿 창 → 학생 사이트 → `1모둠` + 다른 단서 등록
3. 두 화면에서 서로의 `1모둠` 단서가 보이는지 확인
4. 다른 브라우저 또는 다른 시크릿 창 → `2모둠` + 단서 등록
5. 1모둠 화면에서 2모둠 내용이 안 보이는지 확인
6. 교사용 페이지 로그인
7. `모든 모둠 단서 공개`
8. 학생 화면에서 2모둠 단서가 자동으로 나타나는지 확인

이 8단계가 모두 되면 수업용으로 사용할 수 있습니다.
