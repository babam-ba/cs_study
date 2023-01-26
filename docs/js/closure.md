---
sidebar_position: 2
---

# 클로저 (Closure)

> 클로저(Closure)란 무엇이고 왜 사용하는지 설명해주세요 ⭐⭐⭐

MDN은 "클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다." 라고 정의하고 있다.

## 렉시컬 스코프

자바스크립트 엔진은 함수를 어디서 호출했는가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라 한다.

## 함수 객체의 내부 슬롯 `[[Environment]]`

함수는 자신의 내부 슬롯 `[[Environment]]`에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.

이때 자신의 내부 슬롯 `[[Environment]]`에 저장된 상위 스코프의 참조는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킨다. 왜냐하면 함수 정의가 평가되어 함수 객체를 생성하는 시점은 함수가 정의된 환경인 상위 함수가 평가 또는 실행되고 있는 시점이며, 이때 현재 실행 중인 실행 컨텍스트는 상위 함수의 실행 컨텍스트이기 때문이다.

# 클로저와 렉시컬 환경

```js
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

위 예제에서 outer 함수를 호출하면 inner 함수를 리턴하고 outer 함수는 생명주기를 마감한다. 즉, 실행 컨텍스트 스택에서 제거된다.

따라서 outer 함수 내부의 x 변수도 생명주기를 마감하므로 더 이상 x변수에 접근할 길이 없어 보인다.
하지만 innerFunc()을 호출한 결과, outer함수의 지역 변수인 x의 값인 10이 출력된다.

**이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료된 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 `클로저`라고 부른다.**

---

outer 함수가 평가되어 함수 객체를 생성할 때 `[[Environment]]` 내부 슬롯에 상위 스코프인 전역 렉시컬 환경을 저장한다.

![IMG_3316](https://user-images.githubusercontent.com/63364990/214808241-d826e4fb-6ae1-427c-99eb-b1e3160d05ce.jpg)

---

그 후 outer 함수를 호출하면 outer 함수의 렉시컬 환경이 생성되고 `[[Environment]]` 내부 슬롯에 저장되었던 전역 렉시컬 환경을 outer 함수 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 할당한다.  
그리고 inner 함수가 평가되어 자신의 `[[Environment]]` 내부 슬롯에 상위 스코프인 outer 함수의 렉시컬 환경을 저장한다.

![IMG_3317](https://user-images.githubusercontent.com/63364990/214808253-36d2b070-41d7-4f62-9c96-79cbaa90a1d4.jpg)

---

outer 함수의 실행이 종료되면 실행 컨텍스트 스택에서 제거된다. 이때 outer 함수의 실행 컨텍스트는 제거되지만 **outer 함수의 렉시컬 환경까지 소멸하는 것은 아니다.**

outer 함수의 렉시컬 환경은 inner 함수의 `[[Environment]]` 내부 슬롯에 의해 참조되고 있으므로 가비지 컬렉션의 대상이 되지 않는다.

> 가비지 컬렉터는 누군가가 참조하고 있는 메모리 공간을 함부로 해제하지 않는다.

---

스택에서 제거되어도 렉시컬 환경은 유지되는 것을 볼 수 있다.  
![IMG_3318](https://user-images.githubusercontent.com/63364990/214808255-cfc89621-11f2-4de1-9d58-ac4cd234e860.jpg)

---

이제 inner 함수가 호출되면 실행 컨텍스트 스택에 푸시되고, 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에는 inner 함수 객체의 `[[Environment]]` 내부 슬롯에 저장되어 있는 참조 값인 outer 함수의 렉시컬 환경이 할당된다.

![IMG_3319](https://user-images.githubusercontent.com/63364990/214810459-c08cedee-24bf-4594-8142-9837d7f6f714.jpg)

중첩 함수 inner는 외부 함수 outer보다 더 오래 생존했다. 외부 함수보다 더 오래 생존한 중첩 함수는 외부 함수의 생존 여부와 상관없이 자신이 정의된 위치에서 결정된 상위 스코프를 기억한다.

---

이론적으로 자바스크립트의 모든 함수는 상위 스코프를 기억한다. 하지만 모든 함수를 클로저라 칭하지는 않는다.

```js
function foo() {
  const x = 1;

  function bar() {
    const y = 2;
    console.log(y);
  }
  return bar;
}

const bar = foo();
bar();
```

위 예제에서 중첩 함수인 bar는 foo 함수보다 오래 생존하지만 상위 스코프의 어떤 식별자도 참조하고 있지 않다.
이처럼 상위 스코프의 어떠한 식별자도 참조하고 있지 않을 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프를 기억하지 않는다. (메모리 낭비이기 때문)  
따라서 bar 함수는 클로저라고 할 수 없는 것이다.

## 클로저 활용

**클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다. 다시 말해, 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용한다.**

```js
let num = 0;

const increase = function () {
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 예제는 increase함수를 호출할 때마다 num을 하나씩 증가시키는 카운터 예시다. 위 코드는 잘 동작하지만 오류를 발생시킬 가능성을 가지고 있는 코드다.

num 변수는 전역 변수이기 때문에 언제든지 누구나 접근하고 변경할 수 있다.  
따라서 increase 함수만이 변수를 참조하고 변경할 수 있게 하는 것이 바람직하다. 전역 변수인 num을 increase 함수의 지역 변수로 바꾸어 의도치 않은 상태 변경을 방지해보자.

```js
const increase = (function () {
  let num = 0;

  return function () {
    return ++num;
  };
})();

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 실행되면 즉시 실행 함수가 호출되고 반환한 함수가 increase 변수에 할당된다. **increase 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 즉시 실행 함수를 기억하는 클로저다.**

즉시 실행 함수는 호출 이후 소멸되지만 반환한 클로저는 increase 변수에 할당되어 하단에서 호출되고 있다.

즉시 실행 함수는 한 번만 실행되므로 increase가 호출될 때마다 num 변수가 재초기화될 일이 없다. 또한 num 변수는 외부에서 직접 접근할 수 없이 은닉된 private한 변수가 되었다.

**이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 은닉하고, 특정한 함수에게만 상태 변경을 허용하여 상태를 안전하게 관리하기 위해 사용한다.**

### 자주하는 실수

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
// 3 3 3
```

위 예제에서 0 1 2가 차례대로 출력될거라 예상했지만 3 3 3으로 출력된다. var 키워드로 선언한 i 변수는 블록 레벨 스코프가 아닌 함수 레벨 스코프를 갖기 때문에 전역 변수다. 따라서 funcs 배열의 요소로 추가한 함수를 호출하면 전역 변수 i를 참조해서 3만 출력된다.

---

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

다음과 같이 즉시 실행 함수의 인자로 현재 i 값을 전달하여 매개변수 id에 할당한 후 중첩 함수를 반환하고 종료된다. 중첩 함수는 상위 스코프를 기억하는 클로저이고, 매개변수 id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유변수가 되어 그 값이 유지된다.

> 자유변수: 클로저를 포함하고 있는 외부 함수의 인자, 지역변수를 말한다.

> 클로저라는 이름은 자유변수에 함수가 닫혀있다(closed)라는 의미로, 자유변수에 엮여있는 함수라는 뜻이다.

---

또는 let을 사용하는 방법도 있다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (let j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

for문의 코드 블록이 반복 실행될 때마다 새로운 렉시컬 환경이 생성된다. 만약, for 문 내에 정의한 함수가 있다면 함수의 상위 스코프는 for 문의 코드 블록이 반복 실행될 때마다 생기는 새로운 렉시컬 환경이다.
반복될 때마다 식별자의 값을 유지해야 하기 때문에 `funcs[j]()`를 호출해도 0 1 2 값으로 출력된다.
