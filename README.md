# 🌍 지구촌 자원 분배 게임 — QR 입장 + 역할 공개 버전

학생들이 QR로 입장 → 이름 입력 → 모두 모이면 '역할 확인' 버튼이 활성화 → 본인 역할을 공개하는 환경교육 게임입니다.

## 🎯 동작 흐름

### 선생님 화면
1. **인원 설정** (10~30명)
2. **역할 배정 결과** 확인 → "📱 QR 세션 시작" 클릭
3. **로비 화면**에 6자리 코드, QR 코드, 학생용 URL 표시
4. 학생들이 입장하면 **국가별로 학생 이름이 실시간 라벨링**되고 카운터가 올라감
5. 모두 모이면 "🎭 역할 공개하기" 버튼 활성화 → 클릭하면 학생들에게 랜덤 배정

### 학생 화면 (스마트폰)
1. QR 스캔 또는 6자리 코드 입력
2. 이름 입력 → 입장
3. 친구들이 모두 모일 때까지 대기 (실시간 카운트)
4. 선생님이 공개 버튼을 누르면 **"🎭 내 역할 확인하기"** 버튼이 활성화
5. 클릭하면 본인의 국가/역할이 짠 ✨

## ⚙️ Firebase 설정 (한 번만 하면 됩니다)

이 게임은 **Firebase Realtime Database** 무료 플랜으로 동작합니다. 설정에 약 5분 정도 걸려요.

### 1) Firebase 프로젝트 만들기
1. https://console.firebase.google.com 접속 (구글 계정 로그인)
2. **"프로젝트 추가"** 클릭
3. 프로젝트 이름 입력 (예: `earth-game`) → 다음
4. Google 애널리틱스는 **사용 안 함** 선택 (간단하게)
5. **"프로젝트 만들기"**

### 2) Realtime Database 만들기
1. 왼쪽 메뉴에서 **"빌드 → Realtime Database"** 클릭
2. **"데이터베이스 만들기"** 클릭
3. 위치는 `asia-southeast1` 추천 (한국에서 가까움)
4. **"테스트 모드에서 시작"** 선택 → 사용 설정
   - ⚠️ 테스트 모드는 30일 후 만료. 수업용으로는 충분하지만 영구 사용 시 규칙 수정 필요
5. URL이 `https://YOUR-PROJECT-default-rtdb.asia-southeast1.firebasedatabase.app/` 같은 형태로 표시됩니다 — 메모

### 3) 웹 앱 등록
1. 왼쪽 메뉴 **"⚙️ 프로젝트 설정"** 클릭
2. 일반 탭 아래로 스크롤 → **"내 앱"** 영역에서 **`</>` (웹) 아이콘** 클릭
3. 앱 닉네임 입력 (예: `earth-web`) → "앱 등록"
4. **`firebaseConfig` 객체가 화면에 표시됩니다** ← 이것을 복사

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...........",
  authDomain: "earth-game.firebaseapp.com",
  databaseURL: "https://earth-game-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "earth-game",
  storageBucket: "earth-game.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcd........"
};
```

### 4) index.html 파일에 붙여넣기
1. `index.html` 열기 → `firebaseConfig` 검색
2. 파일에 있는 `YOUR_API_KEY` 등이 있는 객체를 **방금 복사한 값으로 통째로 교체**
3. 저장

⚠️ **주의**: `databaseURL`이 자동으로 보이지 않으면, Realtime Database 메뉴에서 URL을 복사해 직접 추가해주세요.

### 5) 호스팅 (학생들도 접근 가능하도록)
QR이 가리키는 URL을 학생들이 열 수 있어야 합니다. 가장 쉬운 방법:

**옵션 A — Netlify Drop (추천, 가장 쉬움)**
1. https://app.netlify.com/drop 접속
2. `index.html` 파일을 화면에 드래그해서 떨어뜨리기
3. 즉시 `https://random-name.netlify.app` 같은 URL이 발급됨
4. 그 URL을 브라우저에서 열고 위 흐름대로 진행

**옵션 B — GitHub Pages**
1. 새 GitHub 저장소 만들고 `index.html` 업로드
2. Settings → Pages → main 브랜치에서 활성화
3. `https://username.github.io/repo-name` 으로 접근

**옵션 C — Firebase Hosting** (이미 Firebase 쓰니까 자연스러움)
1. Firebase Console → Hosting → 시작하기
2. 안내 따라서 `firebase deploy` 한 번
3. `https://your-project.web.app` 발급

## 🧪 테스트하는 법
1. 선생님 PC: 사이트 열기 → 인원 23명 → 역할 배정 → QR 세션 시작
2. 휴대폰: QR 스캔 (또는 같은 URL 직접 입력) → 이름 적고 입장
3. 다른 휴대폰들로 23명까지 채우면 선생님 화면의 '역할 공개' 버튼이 활성화됩니다
4. 클릭하면 모든 학생 휴대폰에서 '내 역할 확인하기' 버튼이 켜짐 → 클릭하면 짠 ✨

## 📝 데이터베이스 규칙 (선택)

수업용으로는 테스트 모드로 충분하지만, 영구 사용하려면 Realtime Database → 규칙 탭에서 아래로 교체하세요:

```json
{
  "rules": {
    "sessions": {
      "$code": {
        ".read": true,
        ".write": true,
        ".validate": "$code.matches(/^[0-9]{6}$/)"
      }
    }
  }
}
```

(공개 게임이라 누구나 읽기/쓰기 가능합니다. 6자리 코드를 모르면 접근 불가)

## 📌 데이터 구조
```
sessions/
  123456/                       (6자리 세션 코드)
    createdAt: 1731234567890
    total: 23
    revealed: false             (역할 공개 여부)
    capacityByOrder: {1:5, 2:3, ...}
    students/
      -ABC.../                  (Firebase push ID)
        name: "홍길동"
        joinedAt: 1731234567899
        role: { order: 7, flag: "🇰🇷", name: "한국인", ... }   (공개 후 추가됨)
```

## ❓ 문제 해결

**Q. QR을 스캔했는데 페이지가 안 열려요**
→ Netlify/GitHub Pages 등으로 호스팅했는지 확인. 로컬 `file://` URL은 다른 기기에서 못 봅니다.

**Q. 학생이 이름 입력해도 카운트가 안 올라가요**
→ Firebase 설정값이 정확히 들어갔는지 확인. 브라우저 개발자도구(F12) Console 탭에 빨간 에러가 있는지 보세요.

**Q. 30명 넘는 학급은요?**
→ 현재는 10~30명으로 제한되어 있어요. JavaScript 코드 상단의 `min="10" max="30"`을 수정하면 됩니다.

**Q. 같은 학생이 두 번 입장하면 어떻게 되나요?**
→ 별도 슬롯으로 들어갑니다. 선생님이 로비 화면에서 중복 이름이 보이면 안내해주세요. (이름 중복 검사는 학교 환경 특성상 일부러 넣지 않았습니다.)
