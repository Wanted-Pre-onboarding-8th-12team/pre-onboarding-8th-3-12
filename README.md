# 동료학습을 통해서 인턴쉽 선발 과제 Best Practice 만들기

## 📕 개요



### 과제 목표

> 한국임상정보 검색영역 클론하기

### 기간

2023.01.10 ~ 2023.01.13

---

## 👨‍👩‍👧‍👦 Members

|                                              류지창                                              |                                             박준하                                              |                                             백광천                                              |                                             유제원                                              |                                             정세연                                              |                                             조영일                                              |
| :----------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------: |
| <img src="https://avatars.githubusercontent.com/u/104156381?s=70&v=4" width="100" height="100"/> | <img src="https://avatars.githubusercontent.com/u/85827017?s=70&v=4" width="100" height="100"/> | <img src="https://avatars.githubusercontent.com/u/82658528?s=70&v=4" width="100" height="100"/> | <img src="https://avatars.githubusercontent.com/u/96014828?s=70&v=4" width="100" height="100"/> | <img src="https://avatars.githubusercontent.com/u/79056677?s=70&v=4" width="100" height="100"/> | <img src="https://avatars.githubusercontent.com/u/86599495?s=70&v=4" width="100" height="100"/> |
|                           [RyuJiChang](https://github.com/RyuJiChang)                            |                            [harseille](https://github.com/harseille)                            |                             [back0202](https://github.com/back0202)                             |                               [LLSJYY](https://github.com/LLSJYY)                               |                               [n0eyes](https://github.com/n0eyes)                               |                            [young1the](https://github.com/young1the)                            |

---

## 🖥 Demo
gif 시연 내용 추가

---

## ⚡️ 사용 라이브러리

- react: 18.2.0
- styled-reset: 4.4.5
- styled-components: 5.3.6
- axios: 1.2.2
- typescript: 4.9.3
---

## ✅ 요구 사항 체크리스트

1. 구현 사항

- [x] 한국상정보 검색영역을 클론하기
- [x] 질환명 검색시 API 호출 통해서 검색어 추천 기능 구현
  - [x] 사용자가 입력한 텍스트와 일치하는 부분 볼드처리
  - [x] 검색어가 없을 시 “검색어 없음” 표출
- [x] API 호출 최적화
  - [x] API 호출별로 로컬 캐싱 구현
    - [x] 캐싱 기능을 제공하는 라이브러리 사용 금지(React-Query 등)
    - [x] 캐싱을 어떻게 기술했는지에 대한 내용 README에 기술
  - [x] 입력마다 API 호출하지 않도록 API 호출 횟수를 줄이는 전략 수립 및 실행
    - [x] README에 전략에 대한 설명 기술
  - [x] API를 호출할 때 마다 console.info("calling api") 출력을 통해 콘솔창에서 API 호출 횟수 확인이 가능하도록 설정
- [x] 키보드만으로 추천 검색어들로 이동 가능하도록 구현
  - [x] 사용법 README에 기술
- [x] 언어 : JavaScript / TypeScript (가급적 선택)
- [x] 사용가능한 기술
  - [x] 전역 상태관리 라이브러리(Redux 등)
    - [x] 단, 캐싱 기능이 포함되지 않은 것으로 제한
  - [x] 스타일 관련 라이브러리(styled-components, emotion, UI kit, tailwind, antd 등)
  - [x] HTTP Client(axios 등)

### 🥲 Best Practice는 아니었지만 고민한 것과 개발 내용

> 내용이 방대하여 추가적인 링크로 전달드립니다.

- [류지창 Trouble Shooting Log](https://www.notion.so/b53badc75edb4edc81c5990cb135efd0)
- [박준하 Trouble Shooting Log](https://www.notion.so/5dbd0179028240898238e0c8560a4f28)
- []

## 👍 Best Practice

Best Practice는 [영일](https://github.com/young1the)님의 코드를 선정했습니다.

영일님의 코드의 특별한 점을 아래에 사항에 설명드리겠습니다.

### 1. 입력한 텍스트와 일치하는 부분 볼드처리

유저가 검색한 값인 `value` 를 기준으로 `split()` 하여 나눈 문자열 배열을 `map()` 을 이용해서 붙이는 방법으로 볼드처리 했습니다.

### 2. API 호출 최적화

#### 2-1. API 호출별로 로컬 캐싱 구현

```jsx
// in SearchWorker
async getSickInfos(query: string) {
  const Cache = new CacheStorage("sick");
  const cachedData = await Cache.match(query);
  if (cachedData) {
    return cachedData;
  }
	const params: TSickParams = { q: query };
  const requestedData = await getSickInfoByQueryString(params);
  Cache.put(query, requestedData);
  return requestedData;
}
```

`Request`와 `Response`를 쌍으로 저장할 수 있는 `CacheStroage`를 이용해서 api를 호출하기 전에 이미 같은 `Request` 에 대한 `Response` 가 있는 지 확인하고, `Request` 가 없을 때에만 `getSickInfoByQueryStrings` 를 호출해서 api요청을 보내도록 처리했습니다.

#### 2-2. 입력마다 API 호출하지 않도록 API 호출 횟수를 줄이는 전략 수립 및 실행

```jsx
useEffect(() => {
    let timer: NodeJS.Timeout;
    if (!value) setSearchState("None");
    else {
      timer = setTimeout(() => {
        search(value);
      }, delay);
    }
    return () => {
      if (timer) clearTimeout(timer);
    };
  }, [value]);
```

`<input/>`의 value 값인 `value`가 바뀔 때마다, `useEffect`가 실행되고, `value`가 빈 문자열이 아닌 경우에는 `setTimeout()` 을 이용해서 `delay`이후에 api를 호출하거나 캐싱된 결과를 받아오는 `search` 함수가 실행되도록 했고 `value`가 다시 바뀌어서 재 렌더링 될 경우에 이전에 설정된 `timer`를 `clearTimeout()` 해서 **debounce** 처리 했습니다.

### 3. 키보드만으로 추천 검색어들로 이동 가능하도록 구현

```jsx
const valueRef = useRef(value);
const [tabIndex, setTabIndex] = useState<number>(-1);
const onKeyDownHandler = useCallback(
    (ev: KeyboardEvent) => {
      if (result) {
        switch (ev.key) {
          case "ArrowDown":
            setTabIndex((prev) => 
							(prev === result?.length - 1 ? 0 : prev + 1));
            break;
          case "ArrowUp":
            setTabIndex((prev) => (prev === 0 ? -1 : prev - 1));
            break;
          case "Enter":
            alert(valueRef.current);
					// .....
        }
      }
    },
    [result, valueRef.current]
  );
```

`<SearchSection/>` 내부에 `tabIndex`라는 상태값을 두고, `<input/>`의 `onKeyDown`이벤트로 `tabIndex`값을 변경할 수 있는 함수를 넘겨주었습니다. 

```jsx
result.map((ele, index) => { return
<SearchItem
	// ...props
	isFocused={index === tabIndex}
	focusValueRef={valueRef}
/> })
const SearchItem = (props) => {
	if (isFocused) focusValueRef.current = sickCd;
// ...
}
```

`<SearchItem/>` 에서 `tabIndex`와 자신의 index값이 같을 때, `isFocused`가 `true`가 되고 이 경우에 `valueRef`에 `sickCd`를 담아주어서 “Enter”키가 입력되었을 때, 해당 `sickCd`를 이용해서 `navigate()`또는 modal창을 띄워주는 등의 작업을 할 수 있도록 처리했습니다.

## 📝 문서
[회의록](https://absorbed-leek-405.notion.site/2-best-practice-9d77b76380b14b8d9357e07dd8e80e17)
