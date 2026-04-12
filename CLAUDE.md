# JP Daily - 일본어 데일리 학습 앱

## 프로젝트 개요
단일 HTML 파일 웹앱. Anthropic Claude API를 사용하여 일본어 학습 데이터를 생성하고,
localStorage에 누적 저장하여 복습 및 퀴즈를 제공한다.

## 파일 구조
```
jp-daily/
├── CLAUDE.md          # 이 파일
├── index.html         # 메인 앱 (단일 파일)
└── prompts.js         # API 프롬프트 정의 (index.html에 인라인 포함)
```

## 핵심 변경 이력
### v2 → v3 개선 사항
1. **후리가나(루비 텍스트)**: 모든 일본어 텍스트에 히라가나 읽기 표시
2. **문장 단어별 분해**: 한 문장 탭에서 단어/동사/형용사/조사를 개별 설명
3. **카타카나 2개/일**: 하루에 2개씩 생성 (fetchKatakana 1회 호출 = 2개 반환)
4. **가타카나 페어 표시**: 2개가 한 세트로 카드에 나란히 표시

## API 모델
- `claude-sonnet-4-20250514` 고정 사용

## localStorage 키
- `jp_daily_v3` (버전 변경 시 키 변경할 것)

## 프롬프트 설계 원칙
- 반드시 JSON만 반환 (백틱, 설명 텍스트 금지)
- 기존 데이터와 중복 방지 (used 배열 전달)
- 후리가나는 HTML ruby 태그용 pairs 배열로 반환

## 후리가나 렌더링
```javascript
// pairs: [{base:"食", ruby:"た"}, {base:"べる", ruby:""}]
function renderRuby(pairs) {
  return pairs.map(p =>
    p.ruby ? `<ruby>${p.base}<rt>${p.ruby}</rt></ruby>` : p.base
  ).join('');
}
```

## 단어 분해 구조 (한 문장 탭)
```json
{
  "tokens": [
    {"text":"今日","reading":"きょう","type":"명사","meaning":"오늘"},
    {"text":"は","reading":"は","type":"조사","meaning":"~은/는"},
    {"text":"いい","reading":"いい","type":"형용사","meaning":"좋은"},
    {"text":"天気","reading":"てんき","type":"명사","meaning":"날씨"},
    {"text":"です","reading":"です","type":"정중체","meaning":"입니다"},
    {"text":"ね","reading":"ね","type":"종조사","meaning":"~네요(동의/공감)"}
  ]
}
```

## 수정 시 주의사항
- 백그라운드 프리페치 금지 - 버튼 클릭 시만 1회 fetch
- 429/401 에러 시 한국어 안내 메시지 표시
- JSON 파싱 전 마크다운 백틱 제거 필수
- `position: fixed` 사용 금지 (모달은 flex overlay로 처리)
