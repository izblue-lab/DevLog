# 초보자를 위한 로컬 AI (Ollama) 웹 브라우저 연동 가이드

이 문서는 웹사이트(웹앱)에서 내 컴퓨터에 설치된 로컬 AI 모델(Ollama)을 연동할 때 발생하는 오류를 해결하고, 매번 복잡하게 환경 설정을 하는 번거로움 없이 편하게 사용하기 위한 초보자용 가이드입니다.

---

## 1. 매일 사용하는 3단계 실행 순서 (TL;DR)

어려운 셋업은 모두 끝났습니다! 매일 로컬 AI를 사용할 때는 아래 순서대로만 하시면 됩니다.

1. **Ollama 앱 실행**: 맥 화면 상단 메뉴바에 Ollama 아이콘이 잘 떠 있는지 확인합니다.
2. **웹사이트 접속**: 로컬 AI를 연동해서 사용할 웹앱(웹사이트)에 접속합니다.
3. **브라우저 보안 허용 (최초 1회)**:
   - 주소창 왼쪽의 **조절기(또는 자물쇠) 아이콘**을 클릭합니다.
   - **`기기에 있는 앱` (또는 `Apps on your device`)** 설정을 찾아 **허용(ON)**으로 켭니다.
   - ⚠️ **중요**: 설정을 켠 뒤에는 반드시 웹페이지를 **새로고침(F5 또는 Cmd+R)** 해주세요!

---

## 2. [Mac 전용] 딱 1초 만에 끝내는 영구 자동화 셋업

매번 컴퓨터를 켤 때마다 터미널을 열고 설정을 입력하지 않고, 맥이 켜질 때마다 자동으로 브라우저 연동 권한(CORS)을 부여하도록 만드는 방법입니다.

1. 맥에서 **터미널(Terminal) 앱**을 실행합니다. (Cmd + Space 누르고 '터미널' 검색)
2. 아래의 명령어를 **전체 복사해서 그대로 붙여넣고 엔터(Enter)**를 누릅니다.

```bash
# 시작 프로그램 등록 폴더 생성 후, 설정 plist 파일을 자동으로 심고 적용하는 마법의 명령어입니다.
mkdir -p ~/Library/LaunchAgents && cat > ~/Library/LaunchAgents/com.ollama.origins.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.ollama.origins</string>
  <key>ProgramArguments</key>
  <array>
    <string>launchctl</string>
    <string>setenv</string>
    <string>OLLAMA_ORIGINS</string>
    <string>*</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>ServiceIPC</key>
  <false/>
</dict>
</plist>
EOF
launchctl load ~/Library/LaunchAgents/com.ollama.origins.plist
launchctl setenv OLLAMA_ORIGINS "*"
echo "🎉 영구 자동화 설정 완료! Ollama 앱을 재시작해주세요!"
```

3. **Ollama 앱 재시작**:
   - 상단 메뉴바의 Ollama 아이콘을 눌러 **`Quit Ollama`**로 완전히 종료합니다.
   - 응용 프로그램(Applications) 폴더나 Launchpad에서 **Ollama 앱을 다시 실행**합니다.

> [!TIP]
> **맥이 켜질 때 Ollama가 자동으로 실행되도록 설정하는 방법**
> 1. 맥 화면 좌측 상단의 **애플 메뉴() ➔ [시스템 설정]**을 클릭합니다.
> 2. 왼쪽 사이드바에서 **[일반]**을 누른 뒤, **[로그인 항목]** 메뉴로 이동합니다.
> 3. **'로그인할 때 열기'** 목록 아래의 **[＋]** 버튼을 클릭합니다.
> 4. 응용 프로그램(Applications) 폴더에서 **Ollama** 앱을 선택하여 추가해 줍니다.

---

## 3. 웹앱에서 모델 바꾸기 (모델 덮어쓰기/엘리어싱)

웹사이트 소스코드 내에 호출하는 모델명이 고정되어 변경할 수 없는 경우, Ollama 내부에서 이름표를 바꿔치기하여 내가 원하는 고성능 모델을 작동시킬 수 있습니다.

* **원하는 모델 복사하기 (예시: `gemma4:e4b` 모델을 기본 모델로 덮어쓰기)**
  ```bash
  # 1. 웹사이트가 요구하는 기존 모델명(예: qwen3.5-flash:latest)이 있다면 삭제
  ollama rm qwen3.5-flash:latest
  
  # 2. 내가 진짜 돌리고 싶은 모델(예: gemma4:e4b)을 해당 이름표로 복사
  ollama cp gemma4:e4b qwen3.5-flash:latest
  ```
  *(모델의 내부 ID가 동일하게 묶이므로 용량은 이중으로 차지하지 않습니다!)*

---

## 4. 자주 묻는 질문 (FAQ)

### Q. '기기에 있는 앱' 권한 설정이 조절기 아이콘 메뉴에 안 보여요.
크롬 설정창에서 직접 수동으로 추가하실 수 있습니다.
1. 크롬 우측 상단 **점 3개(⋮)** 클릭 ➔ **설정**으로 들어갑니다.
2. **개인정보 및 보안** ➔ **사이트 설정**으로 이동합니다.
3. 맨 아래 **추가 권한** ➔ **기기에 있는 앱** 항목을 클릭한 뒤, 해당 웹사이트 주소가 허용 상태인지 확인합니다.

### Q. 설정이 잘 되었는지 터미널에서 어떻게 확인하나요?
터미널을 열고 아래 명령어를 입력했을 때 `*`가 출력되면 정상적으로 연동 권한이 설정된 상태입니다.
```bash
launchctl getenv OLLAMA_ORIGINS
```
