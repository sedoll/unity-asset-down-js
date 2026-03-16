# 🎁 유니티 중화권 무료 에셋 자동 추가 스크립트 사용 설명서

3월 31일에 대거 판매 중단되는 유니티 스토어의 중화권 무료 에셋(약 250여 개)을 내 라이브러리에 자동으로 일괄 추가하는 브라우저 콘솔용 JavaScript 실행 가이드입니다.

## ⚠️ 사전 준비 (매우 중요)
스크립트가 정상적으로 작동하기 위해 아래 세 가지를 먼저 완료해 주세요.

1. **로그인**: [Unity Asset Store](https://assetstore.unity.com/)에 접속하여 본인 계정으로 로그인합니다.
2. **팝업 차단 해제**: 스크립트가 새 창을 띄워 작업을 수행하므로, 크롬 주소창 우측 끝의 팝업 차단 아이콘을 눌러 **`항상 허용`**으로 변경합니다.
3. **번역 확장 프로그램 비활성화**: 'Immersive Translate' 등의 페이지 번역 확장 프로그램이 켜져 있으면 스크립트와 충돌(에러)이 발생할 수 있으니 작업이 끝날 때까지 잠시 꺼둡니다.

---

## 🚀 실행 순서

### 1단계: 에셋 목록 데이터 복사하기
1. [에셋 JSON 데이터 링크](https://vanquish3r.github.io/greater-china-unity-assets/data.json)를 클릭하여 엽니다.
2. 화면에 나타난 엄청나게 긴 텍스트를 **전체 선택(`Ctrl + A`)** 한 뒤 **복사(`Ctrl + C`)** 합니다.

### 2단계: 개발자 도구 콘솔 열기
1. 유니티 에셋 스토어 탭으로 돌아와서 키보드의 `F12`를 눌러 **개발자 도구**를 엽니다.
2. 상단의 **Console(콘솔)** 탭을 클릭합니다.
3. *(선택)* 만약 입력창에 노란색으로 `allow pasting(붙여넣기 허용)` 경고가 뜬다면, 입력창에 직접 `allow pasting`이라고 타이핑하고 `Enter`를 눌러 보안을 해제합니다.

### 3단계: 스크립트 준비 및 실행
1. 콘솔 입력창에 아래의 코드를 먼저 복사해서 붙여넣습니다. (**아직 Enter를 누르지 마세요!**)

```javascript
// 1. 아래 '여기에_데이터_붙여넣기' 글자를 지우고, 1단계에서 복사한 데이터를 붙여넣으세요.
const rawData = 여기에_데이터_붙여넣기;

// 2. 붙여넣기가 완료되면 Enter를 눌러 실행하세요.
;(async () => {
    console.log("📦 에셋 추가를 시작합니다...");
    
    // 무료 에셋 URL만 추출
    const freeAssets = rawData.filter(item => item.IsFree && item.AssetURL).map(item => item.AssetURL);
    console.log(`총 ${freeAssets.length}개의 무료 에셋을 순차적으로 추가합니다. (작업이 끝날 때까지 메인 창을 닫지 마세요!)`);
    
    for (let i = 0; i < freeAssets.length; i++) {
        console.log(`[${i + 1} / ${freeAssets.length}] 처리 중...`);
        
        // 팝업 창 열기
        const win = window.open(freeAssets[i], '_blank', 'width=800,height=600');
        await new Promise(r => setTimeout(r, 6000)); // 로딩 대기 (6초)
        
        try {
            const buttons = Array.from(win.document.querySelectorAll('button'));
            const addBtn = buttons.find(b => b.innerText.match(/Add to My Assets|내 에셋에 추가/i));
            
            if (addBtn && !addBtn.disabled) {
                addBtn.click();
                await new Promise(r => setTimeout(r, 2000)); 
                
                // 약관 승인 버튼 클릭 (Accept, 동의, 승인 모두 대응)
                const acceptBtn = Array.from(win.document.querySelectorAll('button')).find(b => b.innerText.match(/Accept|동의|승인/i));
                if (acceptBtn) acceptBtn.click();
                
                await new Promise(r => setTimeout(r, 2000)); 
            }
        } catch(e) {
            console.log("요소 접근 에러 (또는 이미 처리된 에셋). 다음으로 넘어갑니다.");
        }
        
        // 팝업 닫기 및 대기
        win.close();
        await new Promise(r => setTimeout(r, 1500)); 
    }
    console.log("🎉 모든 무료 에셋 추가 작업이 완료되었습니다!");
})();

이제 아래 순서대로 진행합니다.

2. 코드 첫 번째 줄의 **`여기에_데이터_붙여넣기`**라는 글자를 지우고, 아까 1단계에서 복사해 둔 긴 데이터를 그 자리에 붙여넣기(`Ctrl + V`) 합니다.
3. **`Enter` 키를 눌러 스크립트를 실행**합니다.

### 4단계: 기다리기
* 스크립트가 실행되면 혼자서 팝업 창을 띄우고 버튼을 누른 뒤 창을 닫는 과정을 반복합니다.
* 250여 개의 작업이 모두 끝날 때까지 웹 브라우저를 켜두고 다른 작업을 하시면 됩니다. (콘솔 창에 완료 메시지가 뜰 때까지 메인 창을 닫지 마세요.)

---

## 🛠️ 자주 발생하는 오류 해결 (Troubleshooting)

* **Q. 콘솔에 `Cannot read properties of null (reading 'close')` 에러가 나면서 멈춰요!**
  * A: 브라우저가 새 창(팝업)을 띄우는 것을 차단했기 때문입니다. 주소창 우측에서 팝업 차단을 '항상 허용'으로 바꾸고, 새로고침(`F12`) 후 다시 실행하세요.
* **Q. 중간에 에셋 추가 버튼이 아니라 약관 '승인' 버튼에서 창이 멈춰 있어요!**
  * A: 본 설명서에 적힌 최종 코드는 'Accept', '동의', '승인' 버튼을 모두 인식하도록 수정되어 있습니다. 이 코드를 사용하시면 정상적으로 승인 처리됩니다.
* **Q. 코드를 실행했더니 `is not a function` 같은 에러가 나요!**
  * A: 데이터를 붙여넣는 과정에서 코드의 문법이 꼬였을 수 있습니다. 새로고침 후, 복사한 코드가 위 예시 코드처럼 `;(async () => {` 형태로 잘 시작하는지 확인하고 다시 붙여넣어 보세요.