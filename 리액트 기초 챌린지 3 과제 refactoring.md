## before
```js
<script type="text/babel">
    const App = () => {
      const [num1, setNum1] = React.useState("");
      const [num2, setNum2] = React.useState("");
      const [index, setIndex] = React.useState("");
      const [result, setResult] = React.useState("");
      
      const handleValue = (e) => {
        return e.target.name === "num1"
          ? setNum1(e.currentTarget.value)
          : setNum2(e.currentTarget.value);
      };

      const handleSelect = (e) => {
        setIndex(e.currentTarget.value);
      };

      const handleResult = () => {
        const n1 = parseInt(num1);
        const n2 = parseInt(num2);
        let res = 0;
        switch (index) {
          case "+":
            res = n1 + n2;
            break;
          case "-":
            res = n1 - n2;
            break;
          case "*":
            res = n1 * n2;
            break;
          case "/":
            res = n1 / n2;
            break;
          default:
            res = 0;
        }
        setResult(res);
      };
      return (
        <div className="container">
          <h1>🦖 Calculator 🦖</h1>
          <input
            type="number"
            placeholder="number"
            name="num1"
            value={num1}
            onChange={handleValue}
          />
          <input
            type="number"
            placeholder="number"
            name="num2"
            value={num2}
            onChange={handleValue}
          />

          <select value={index} onChange={handleSelect}>
            <option value="1">👇🏻Select Operation</option>
            <option value="+">더하기</option>
            <option value="-">빼기</option>
            <option value="/">나누기</option>
            <option value="*">곱하기</option>
          </select>
          <button type="button" onClick={handleResult}>
            계산하기
          </button>
          <h2>🦖결과 : {result}</h2>
        </div>
      );
    };
    const root = document.getElementById("root");
    ReactDOM.render(<App />, root);
  </script>
```
## after
```js
<script type="text/babel">
	const operators = [
		{ key: "addition", text: "+", fn: (x, y) => x + y },
		{ key: "subtraction", text: "-", fn: (x, y) => x - y },
		{ key: "multiplication", text: "*", fn: (x, y) => x * y },
		{ key: "division", text: "/", fn: (x, y) => x / y },
	];
	const App = () => {
		const [num1, setNum1] = React.useState("");
		const [num2, setNum2] = React.useState("");
		const [index, setIndex] = React.useState("");
		const [result, setResult] = React.useState("");
		const [allReady, setAllReady] = React.useState(true);

		const handleValue = (e) => {
			const {
				currentTarget: { name, value },
			} = e;
			// 한번 계산 후에 값을 지워 빈칸일 때 버튼의 disabled가 false라서 넣었
			if (value === "") setAllReady(true);
			// 처음 계산할 때 연산자를 선택하지 않고 
			// input에 값을 모두 지정하면 disabled가 false로 된다
			// 원래는 value !== "" 이었는데 조건 추가
			if (value !== "" && index !== "") setAllReady(false);
			return name === "num1" ? setNum1(value) : setNum2(value);
		};

		const handleSelect = (e) => {
			// 멋있어
			const {
				target: { value },
			} = e;
			setIndex(value);

			if (value === "") {
				setResult("연산자를 선택해주세요");
				setAllReady(true);
			} else if (num1 === "" || num2 === "") {
				setAllReady(true);
			} else {
				setAllReady(false);
			}
		};

		const handleResult = () => {
			const selectedOperator = operators.find((op) => op.key === index);
			const n1 = parseInt(num1);
			const n2 = parseInt(num2);
			if (num2 === "0" && selectedOperator.key === "division") {
				return setResult("0말고 다른 숫자를 입력해주세요");
			}

			setResult(selectedOperator.fn.bind(null, n1, n2));
		};

		const handleReset = () => {
			setNum1("");
			setNum2("");
			setIndex("");
			setResult("");
			setAllReady(true);
		};

		return (
			<div className="container">
				<h1>🦖 Calculator 🦖</h1>
				<input
					type="number"
					placeholder="number"
					name="num1"
					value={num1}
					onChange={handleValue}
				/>
				<input
					type="number"
					placeholder="number"
					name="num2"
					value={num2}
					onChange={handleValue}
				/>

				<select value={index} onChange={handleSelect}>
					<option value={null}>👇🏻Select Operation</option>
					{operators.map((item) => (
						<option key={item.key} value={item.key}>
							{item.text}
						</option>
					))}
				</select>
				<button type="button" onClick={handleResult} disabled={allReady}>
					계산하기
				</button>
				<button type="button" onClick={handleReset}>
					초기화
				</button>
				<h2>🦖결과 : {result}</h2>
			</div>
		);
	};
	const root = document.getElementById("root");
	ReactDOM.render(<App />, root);
</script>
```
1. event 객체를 그냥 사용하지 않고 구조분해할당하여 사용
2. 리셋버튼 구현
3. 버튼에 disabled속성을 이용해 조건 미달 시 접근 ㄴㄴ
	- 모든 칸에 숫자와 연산자가 들어가야 함, 하나라도 빠지면 안됨
	- 연산자를 선택해야 함(+,-,/,*)
	- 분모가 0이면 0말고 다른 숫자 쓰라고 메세지
4. switch문 제거 : 별로 멋없어 보여
```js
// 연산자 관련 객체 데이터를 만들고
const operators = [
	{ key: "addition", text: "+", fn: (x, y) => x + y },
	{ key: "subtraction", text: "-", fn: (x, y) => x - y },
	{ key: "multiplication", text: "*", fn: (x, y) => x * y },
	{ key: "division", text: "/", fn: (x, y) => x / y },
];
// 해당 데이터를 순회해서 option태그 그리기
{operators.map((item) => (
  <option key={item.key} value={item.key}>
    {item.text}
  </option>
))}
// 마지막으로 switch 문 대신 연산자에 있는 함수를 이용해서 처리
const 선택한_연산자 = operators.find((op) => op.key === operator);
setResult(선택한_연산자.fn.bind(null, 첫_번째_숫자, 두_번째_숫자));
```
위와 같이 로직을 가져가면 option을 추가 됐는데 switch-case를 깜빡하고 누락하거나 하는 개발자 실수를 줄일수 있을 것 같고, 조건문을 최소화 해서 로직을 만들수 있을 것 같다고 한다
[bind()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
## 피드백 후
```js
<script type="text/babel">
	const App = () => {
		const [num1, setNum1] = React.useState("");
		const [num2, setNum2] = React.useState("");
		const [index, setIndex] = React.useState("");
		const [result, setResult] = React.useState("");
		const [allReady, setAllReady] = React.useState(true);
		const hasKorean = /[\uAC00-\uD7A3]/;

		// 별도의 함수로 분리하여 나누기 연산 시 0으로 나누려는 경우를 효과적으로 처리
		const safeDivision = (n1, n2) => {
			return n2 === 0 ? "0으로는 나눌 수 없습니다." : n1 / n2;
		};

		const operators = [
			{ key: "addition", text: "+", fn: (x, y) => x + y },
			{ key: "subtraction", text: "-", fn: (x, y) => x - y },
			{ key: "multiplication", text: "*", fn: (x, y) => x * y },
			{ key: "division", text: "/", fn: safeDivision },
		];

		const handleValue = (e) => {
			const {
				currentTarget: { name, value },
			} = e;
			// 한번 계산 후에 값을 지워 빈칸일 때 버튼의 disabled가 false라서 넣었
			if (value === "") setAllReady(true);
			// 처음 계산할 때 연산자를 선택하지 않고 input에 값을 모두 지정하면 disabled가 false로 된다
			// 원래는 value !== "" 이었는데 조건 추가
			if (value !== "" && index !== "") setAllReady(false);
			// 영어는 안 쳐지지만 한글은 가능하기에 조건 추가
			if (num1 === hasKorean || num2 === hasKorean) {
				setResult("한글 ㄴㄴ");
				setAllReady(true);
				return;
			}

			return name === "num1" ? setNum1(value) : setNum2(value);
		};

		const handleSelect = (e) => {
			const {
				target: { value },
			} = e;
			setIndex(value);
			// console.log(value);
			if (value === "👇🏻Select Operation") {
				setResult("연산자를 선택해주세요");
				setAllReady(true);
				return; // 선택한 값이 없을 때 함수를 종료
			}
			// handleValue에 넣어도 될거라 생각했는데 ㄴㄴ, 아예 타자가 안 쳐진다
			if (num1 === "" || num2 === "") {
				setAllReady(true);
				return; // 필수 입력 값이 부족할 때 함수를 종료
			}
			setAllReady(false); // 모든 조건이 충족되면 버튼을 활성화
		};

		const handleResult = () => {
			const selectedOperator = operators.find((op) => op.key === index);
			const n1 = parseInt(num1);
			const n2 = parseInt(num2);

			setResult(selectedOperator.fn.bind(null, n1, n2));
		};

		const handleReset = () => {
			setNum1("");
			setNum2("");
			setIndex("");
			setResult("");
			setAllReady(true);
		};

		return (
			<div className="container">
				<h1>🦖 Calculator 🦖</h1>
				<input
					type="number"
					placeholder="number"
					name="num1"
					value={num1}
					onChange={handleValue}
				/>
				<input
					type="number"
					placeholder="number"
					name="num2"
					value={num2}
					onChange={handleValue}
				/>

				<select value={index} onChange={handleSelect}>
					<option value={null}>👇🏻Select Operation</option>
					{operators.map((item) => (
						<option key={item.key} value={item.key}>
							{item.text}
						</option>
					))}
				</select>
				<button type="button" onClick={handleResult} disabled={allReady}>
					계산하기
				</button>
				<button type="button" onClick={handleReset}>
					초기화
				</button>
				<h2>🦖결과 : {result}</h2>
			</div>
		);
	};
	const root = document.getElementById("root");
	ReactDOM.render(<App />, root);
</script>
```
1. 나누기 일 때 함수를 따로 분리
2. 한글타자를 생각 못 했다. 조건 추가
3. 가장 많은 변화가 생긴 handleSelect
```js
const handleSelect = (e) => {
	const {
		target: { value },
	} = e;
	setIndex(value);
	if (value === "👇🏻Select Operation") {
		setResult("연산자를 선택해주세요");
		setAllReady(true);
		return; // 선택한 값이 없을 때 함수를 종료
	}
	// handleValue에 넣어도 될거라 생각했는데 ㄴㄴ, 아예 타자가 안 쳐진다
	if (num1 === "" || num2 === "") {
		setAllReady(true);
		return; // 필수 입력 값이 부족할 때 함수를 종료
	}
	setAllReady(false); // 모든 조건이 충족되면 버튼을 활성화
};
```
1. else if문을 사용해서 연관성을 한눈에 파악하기 어려워 분리
2. if문에 return 추가
3. 원래는 value의 값을 빈 문자열로 했는데 더 정확한 값이 들어가는게 맞는 것 같다.              TA 왈 : 일반적으로 숫자보다는 문자열로 표현된 연산자를 사용하는 것이 의미를 파악하기 쉽고, 코드를 관리하기에 더 편리합니다. (예: add, subtract, divide, multiply)                      그리고 value의 값을 null 지정하면 태그안에 문자열이 value로 나온다
```html
 <option value={null}>👇🏻Select Operation</option>
```
```js
if(value === "👇🏻Select Operation") {}
```