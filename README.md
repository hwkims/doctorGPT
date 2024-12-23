알겠습니다! 주어진 내용을 반영하여 GitHub README 파일을 다시 작성했습니다. HTML 코드 내에 실제로 포함된 CSS 스타일과 관리자 정보를 반영하여, 보다 정확하게 작성하였습니다.

---

# doctorGPT 🩺🤖

**doctorGPT**는 **AI 의료 진단 서비스**로, 사용자가 입력한 **증상**, **성별**, **나이** 정보를 바탕으로 OpenAI GPT 모델을 이용해 진단을 제공합니다. 또한, 병원 위치를 지도에 표시하여 사용자가 가까운 병원을 쉽게 찾을 수 있게 돕습니다. 이 프로젝트는 하나의 HTML 파일로 구성되어 있으며, **HTML**, **JavaScript**로 작성되었습니다.

## 주요 기능 🚀

- **AI 진단**: 사용자가 입력한 증상, 성별, 나이 정보를 기반으로 GPT-3.5 또는 GPT-4 모델을 사용해 진단을 제공합니다.
- **병원 위치 표시**: 병원 정보가 포함된 CSV 파일을 읽어 **Leaflet.js**를 통해 지도에 병원을 표시합니다.
- **모델 선택**: GPT-3.5와 GPT-4 중에서 원하는 AI 모델을 선택하여 진단을 받을 수 있습니다.

## 데모 🎥

![AI 의료 진단 서비스](https://example.com/demo.gif)  
(위 이미지는 서비스가 어떻게 작동하는지에 대한 데모 예시입니다.)

## 설치 방법 🛠️

이 프로젝트는 **HTML**, **JavaScript**만으로 구성되어 있어 별도의 설치 과정 없이 바로 사용할 수 있습니다.

### 1. 클론 또는 다운로드

```bash
git clone https://github.com/hwkims/doctorGPT.git
cd doctorGPT
```

### 2. OpenAI API 키 설정 🔑

[OpenAI API](https://platform.openai.com/)에 가입하고 **API 키**를 발급받은 후, HTML 파일 내에 API 키를 입력해야 합니다.

### 3. 로컬 실행

HTML 파일을 브라우저에서 열어 실행할 수 있습니다. 별도의 서버 설정 없이 바로 실행 가능합니다.

---

## 코드 설명 📂

### 1. HTML 구조 🧱

HTML 파일은 사용자가 **API 키**, **모델 선택**, **증상**, **성별**, **나이**를 입력하고 **AI 분석 시작** 버튼을 클릭하여 진단을 받을 수 있는 구조로 작성되어 있습니다. 이 부분은 아래와 같이 구성됩니다:

```html
<div class="form-container">
    <label for="api-key">OpenAI API Key</label>
    <input type="text" id="api-key" placeholder="API Key를 입력하세요" />
</div>
```

### 2. 병원 정보 처리 (CSV 파일 읽기) 📊

병원 정보는 `PapaParse` 라이브러리를 이용해 **CSV 파일**에서 읽어옵니다. 각 병원에 대한 **위도**와 **경도**를 지도에 표시합니다. 병원 정보를 `hospitals` 배열에 저장한 후, 지도에 마커를 추가합니다.

```javascript
Papa.parse('medical_institutions.csv', {
    download: true,
    header: true,
    skipEmptyLines: true,
    complete: function(results) {
        results.data.forEach(row => {
            const lat = parseFloat(row['위도']);
            const lon = parseFloat(row['경도']);
            if (!isNaN(lat) && !isNaN(lon)) {
                hospitals.push({
                    name: row['기관명'],
                    lat: lat,
                    lon: lon,
                    url: "http://example.com"  // 실제 URL로 변경 필요
                });
            }
        });
        displayMap();  // 병원 정보를 로드한 후 지도에 표시
    }
});
```

### 3. 지도 표시 (Leaflet.js) 🗺️

병원 정보를 읽은 후, **Leaflet.js** 라이브러리를 사용해 서울을 중심으로 한 지도를 표시합니다. 각 병원에는 마커가 표시되며, 병원 이름과 예약 링크가 팝업으로 제공됩니다.

```javascript
function displayMap() {
    const map = L.map('map').setView([37.56676211, 126.9672019], 13);  // 서울 중심 좌표

    // OpenStreetMap 타일 추가
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(map);

    // 병원 마커 추가
    hospitals.forEach(hospital => {
        L.marker([hospital.lat, hospital.lon])
            .bindPopup(`<b>${hospital.name}</b><br><a href="${hospital.url}" target="_blank">예약 링크</a>`)
            .addTo(map);
    });
}
```

### 4. OpenAI API 호출 (AI 진단) 🔍

사용자가 입력한 **증상**, **성별**, **나이** 정보를 바탕으로 **OpenAI GPT** API를 호출하여 진단을 받습니다. `fetch`를 사용하여 OpenAI API에 POST 요청을 보내고, 응답을 받아서 결과를 화면에 출력합니다.

```javascript
async function askGPT(symptom, gender, age, apiKey, model) {
    const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
            model: model,
            messages: [
                { role: 'system', content: 'You are a helpful assistant.' },
                { role: 'user', content: `성별: ${gender}, 나이: ${age}, 증상: ${symptom}` }
            ]
        })
    });

    const data = await response.json();
    return data.choices[0].message.content.trim();  // AI 응답 반환
}
```

### 5. 이벤트 처리 (버튼 클릭) 💻

"AI 분석 시작" 버튼을 클릭하면, 사용자가 입력한 데이터를 기반으로 AI 진단을 요청하고 결과를 화면에 출력합니다.

```javascript
document.getElementById('submit-btn').addEventListener('click', async () => {
    const apiKey = document.getElementById('api-key').value;
    const symptom = document.getElementById('symptom').value;
    const gender = document.getElementById('gender').value;
    const age = document.getElementById('age').value;
    const model = document.getElementById('model').value;

    if (!apiKey || !symptom || !gender || !age) {
        alert('모든 정보를 입력해주세요!');
        return;
    }

    // OpenAI API 요청
    const answer = await askGPT(symptom, gender, age, apiKey, model);

    // 결과 출력
    document.getElementById('result').innerHTML = `
        <h3>AI 진단 결과</h3>
        <p><strong>질문:</strong> 성별: ${gender}, 나이: ${age}, 증상: ${symptom}</p>
        <p><strong>답변:</strong> ${answer}</p>
    `;
});
```

### 6. CSS 스타일링 🎨

페이지의 UI는 간단하고 직관적인 스타일로 구성되어 있습니다. `#map` ID를 사용해 지도 크기를 설정하고, 각 입력란, 버튼 등의 요소들을 보기 좋게 스타일링합니다. 또한, 버튼 클릭 후 결과를 표시하는 영역도 스타일링 되어 있습니다.

```css
#map { height: 500px; }
body {
    font-family: Arial, sans-serif;
    margin: 20px;
}
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.form-container {
    margin-bottom: 20px;
}
input, select, button {
    margin: 5px;
    padding: 10px;
    font-size: 16px;
}
#result {
    margin-top: 20px;
    font-size: 18px;
}
```

---

## 프로젝트 구조 📁

```
/doctorGPT
    ├── index.html               # 웹 애플리케이션 UI 및 스크립트
    ├── medical_institutions.csv # 병원 정보 CSV 파일
```

---

## 요구 사항 ⚙️

- **브라우저**: 최신 버전의 Chrome, Firefox 또는 Safari
- **API Key**: OpenAI API 키 (필수)
- **인터넷 연결**: 병원 위치 정보를 지도에 표시하기 위해 필요

---

## 기여하기 🤝

이 프로젝트는 오픈 소스입니다. 기여를 원하시면 아래 방법을 통해 기여할 수 있습니다:

- **버그 수정**: 발견된 버그를 수정하고 Pull Request를 보냅니다.
- **기능 추가**: 유용한 기능을 추가하거나 개선 제안을 합니다.
- **테스트**: 다양한 환경에서 테스트하고 버그 리포트 및 피드백을 제공합니다.

---

## 라이센스 📄

MIT 라이센스 하에 제공됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조해주세요.

---

## 연락처 📬

- **프로젝트 관리자**: [hwkims](https://github.com/hwkims)

---

이 README는 `doctorGPT` 프로젝트의 구조와 기능을 쉽게 이해하고 설치할 수
