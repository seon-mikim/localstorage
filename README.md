# Web storage

- 웹 스토리지 객체(web storage object)인 `localStorage`와 `sesessionStorage`는 브라우저 내에 key-value 쌍을 저장할수 있게 해줌

---

## 왜 쿠키를 사용하지 않고 왜 웹 스토리지를 이용해 데이터를 저장할까? 

- 쿠키와 다르게 웹 스토리지 객체는 네트워크 요청 시 서버로 전송되지 않음
  - 이러한 특징로 인해 쿠키보다 더 많은 자료를 보관 가능(대부분 브라우저는 최소 2MB 혹은 그이상의 웹스토리지 객체를 저장 가능) 

- 개발자는 브라우저 내 웹스토리지 구성 방식을 설정 가능

- 서버가 HTTP 헤더를 통해 스토리지 객체를 조작할 수 없음

- 웹 스토리지 객체 조작은 모두 자바스크립트 내에서 수행 가능

- 웹 스토리지 객체는 도메인, 프로토콜, 포트로 정의되는 오리진(origin)에 묶여 있음
  - 따라서 프로토콜, 서브 도메인이 다르면 데이터에 접근 불가

---

## 메서드와 프로퍼티 

두 스토리 객체는 동일한 메서드와 프로퍼티가 제공됨.

- `setItem(key, value)` : 키 - 값 쌍으로 보관(데이터 저장)
- `getItem(key)` : 키에 해당하는 값을 받아(데이터 갖고 오기)
- `removeItem(key)` : 키에 해당 값을 삭제(데이터 삭제)
- `clear()` : 모든 것을 삭제(모든 데이터 삭제) 
- `key(index)` : 인덱스에 해당하는 키를 받아옴 (인덱스를 이용해 해당하는 키 불러오기) 
- `length` : 인덱스에 해당하는 키를 받아옴 (인덱스를 이용해 해당하는 키 불러오기) 

---

## localStorage

주요 기능

- 오리진 같은 경우 데이터는 모든 탭과 창에 공유
- 브라우저나 OS가 재시작하더라도 데이터가 파기 되지 않음

로컬스토리지를 실행해보자

```js
 localStorage.setItem('test', 1)
 alert(localStorage.getItem('test'))

```
![예제 실행시 확인하기](./images/%EB%A1%9C%EC%BB%AC%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80.PNG '위에 예제 실행 사진')

오리진(domain/port/protocol)만 같다면 url 경로는 달라도 동일한 결과를 볼수 있음 

localStorage는 동일한 오리진을 가진 모든 창에서 공유되기 때문이다. 따라서 한 창에 데이터를 설정하면 다른 창에서 변동 사항을 볼 수 있음.

### 일반 객체처럼 사용하기

`localStorage`의 키를 얻거나 설정할때 아래 예제 처럼 일반 객체와 유사하게 사용함

```js
	localStorage.test = 2

	alert(localStorage.test)

	delete localStorage.test
```
하위 호환성으로 이런 방법 지원가능하나 해당 방법 지양

지양하는 이유 

1. 사용자는 length나 toString, localStorage의 내장 메서드를 키로 설정 할수 있으나,
   getItem, setItem은 정상적으로 작동해도, 일반 객체처럼 다룰 때 에러가 발생할 수 있음

2. 데이터를 수정하면 storage 이벤트 발생하는데 이 이벤트는 localStorage를 객체처럼 접근할 때 일어나지 않음 
---
### 키 순회하기 

`localStorage`는 키를 사용해 값을 얻고, 설정, 삭제 가능하다 그렇다면 키나 값의 전체를 얻을 수 있을까?  스토리지 객체는 iterable 객체는 아니나 대신 배열처럼 다루면 키-값을 얻을 수 있음

```js
	for(let i = 0; i < localStorage.length; i++){
	
		let key = localStorage.key(i)
	
	  alert(`${key}:${localStorage.getItem(key)}`)
	}
```
그냥 for in문을 사용하면 필요하지 않은 내장 필드까지 출력됨
=>  for in 문을 사용하려면 hasOwnProperty를 이용해 프로토타입에서 상속받은 필드를 골라내야 합니다

```js
		for(let key in localStorage) {
			
			if (!localStorage.hasOwnProperty(key)) {
					continue; 
			}

			alert(`${key}: ${localStorage.getItem(key)}`);

		}
```

```js
	let keys = Object.keys(localStorage);
	
		for(let key of keys) {
		
		alert(`${key}: ${localStorage.getItem(key)}`);
	
	}
```

위에 예제는 Object.keys는 해당 객체에서 정의한 키만 반환하고 프로토타입에서 상속받은 키는 무시함

---

### 문자열만 사용
`localStorage`의 키와 값은 문자열

숫자나 객체 등 다른 자료형을 사용하게 되면 문자열로 자동 변환

```js
	
	localStorage.user = {name: "John"};
	
	alert(localStorage.user); 

```

JSON을 사용하면 객체 사용가능 
```js
	localStorage.user = JSON.stringify({name: "John"});

	let user = JSON.parse( localStorage.user );
	
	alert( user.name ); // John
```

디버깅 등의 목적으로 스토리지 객체 전체를 문자열로 변환하는 것도 가능

```js
	alert( JSON.stringify(localStorage, null, 2) ) 
```