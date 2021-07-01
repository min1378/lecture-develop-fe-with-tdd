# 프론트엔드 개발과 TDD 

테스트 주도 개발이 이뤄지지 않는다면 잘못된 코드를 작성하기 쉽다.

```javascript
 console.log = 14

14

console.log()

// Uncaught TypeError: console.log is not a function
```

앞의 예시처럼 console.log 함수가 쉽게 덮어씌워질 때가 있다.

그만큼 javascript는 자유롭고 어디로 튈지 모른다.

컴파일러가 없으니 테스트가 최선이다.

테스트 주도 개발(TDD)로 javascript를 견고하게 만들어보자.



### 단위 테스트 (Unit Test)

`단위 (unit)`

​	특정 조건에서 어떻게 작동해야 할지 정의 한 것.

​	대개 **함수**를 테스트

**준비**(arrange), **실행**(act), **단언**(assert) 패턴을 따른다.



### 테스트주도개발(TDD)

적색(Red), 녹색(Green), 리팩터(Refactor) 순환

테스트하기 쉬운 코드

관심사의 분리



### Jasmin(테스트 라이브러리)

두가지 설치 방법이 있다

스탠드얼론

카르마 라이브러리와 함께 자동화

github : https://github.com/jasmine/jasmine/releases/tag/v2.7.0

강의에선 2.7.0 버전을 기준으로 설명



`SpecRunner.html`

```html
<!-- 자스민 라이브러리 파일 -->
<script src="lib/jasmine-2.7.0/jasmine.js"></script>
<script src="lib/jasmine-2.7.0/jasmine-html.js"></script>
<script src="lib/jasmine-2.7.0/boot.js"></script>

<!-- 소스 코드 -->
<script src="src/Player.js"></script>
<script src="src/Song.js"></script>

<!-- 테스트 코드 -->
<script src="spec/SpecHelper.js"></script>
<script src="spec/PlayerSpec.js"></script>
```



**테스트 러너(Test Runner)**란

- 재스민, 소스, 테스트 코드를 실행하는 녀석
- 스탠드 얼론으로 설치한 자스민은 HTML 파일이 테스트 러너.
- (테스트 자동화를 하려면 테스트 러너인 카르마(Karma)와 함께 사용할 수 있음.)

강사님 레파지토리 : https://github.com/jeonghwan-kim/lecture-develop-fe-with-tdd 에서 클론 후

git checkout -f install-jasmine 명령어를 통해 브랜치 변경



index.html을 통해 테스트 결과를 확인할 수 있음.

describe => 테스트 꾸러미를 만들 때 사용. 함수 하나하나 테스트할 때 사용.

it => 테스트 케이스를 만들 때 사용

it(함수 기능의 스펙, 테스트 코드를 구현할 수 있는 함수)



### 테스트할 수 없는 코드 예시.

```javascript
<button onClick"counter++; countDisplay()">증가</button>
<span id="counter-display">0</span>

<script>
var counter = 0;
function countDisplay(){
	var el = document.getElementById('counter-display');
	el.innerHTML = counter;
}
```

1. 관심사의 분리

   - 클릭 이벤트 처리기를 인라인 형태로 정의됨.

     ```
     <button onClick"counter++; countDisplay()">증가</button>
     ```

     하나의 코드는 한가지 역할만 해야한다.

2. 재사용성이 떨어짐

   - counter로 전역 공간을 어지럽힌다.

     `var counter = 0;`

     횟수를 표시하는 span id를 displayCount 함수에서 하드코딩한 점.

     `var el = document.getElementById('counter-display')`

     확장에는 열려있고, 변경에는 닫혀있어야 한다.



### 어떻게 하면 테스트할 수 있을까?



1. 코드를 UI에서 완전히 분리
   - HTML에서 JS 코드를 떼어내면 비즈니스 로직만 테스트할 수 있음
2. 자바스크립트를 별도 파일로 분리
   - 다른곳에서도 재사용할 수 있고 테스트하기 쉬워진다.



### 모듈 패턴

모듈패턴은 함수로 데이터를 감추고, 모듈 API를 담고 있는 객체를 반환하는 형태.(JS에서 가장 많이 사용되는 패턴.)

1. 임의 함수를 호출하여 생성하는 모듈과
2. 즉시 실행 함수(IIFE) 기반의 모듈이 있다.



```javascript
// 임의 모듈 패턴

// 이름 공간으로 활용한다.

var App = App || {};

// 이름공간에 함수를 추가한다. 의존성있는 God 함수를 주입
App.Person = function (God) {
  var name = God.makeName();
  return {
    getName: function () {
      return name;
    },
    setName: function (newName) {
      name = newName;
    },
  };
};

// 이렇게 사용한다.

const person = App.person(God);
person.getName();
// 2. 즉시 실행 함수(IIFE) 모듈 패턴 (싱글톤 인스턴스가 됨)

var App = App || {};
App.person = (function () {
  let name = "";
  return {
    getName(God) {
      name = name || God.makeName();
      return name;
    },
    setName(newName) {
      name = newName;
    },
  };
})();
// 함수 선언 즉시 실행된다. 싱글톤이다.
App.Person.getName(god)
```

### 모듈 생성 원칙

1. 단일 책임 원칙에 따라 모듈은 한 가지 역할만 한다.

   그 역할만 집중함으로서 모듈을 더욱 튼튼하게 만든다.

   테스트 하기도 쉬움

2.  모듈 자신이 사용할 객체가 있다면 의존성 주입 형태로 제공한다.

   또는 팩토리 주입형태로 제공한다.

   테스트하기도 쉬움



**클릭 카운터 모듈**

ClickCounter는 카운터 데이터를 다루는 모듈이다.

전역 공간에 있는 counter변수를 ClickCounter 안에서 관리하자.



### 첫번째 스펙

"ClickCounter 모듈의 getValue()는 카운터 값을 반환한다"



git checkout --force ClickCounter-spec-1



`ClickCounter.spec.js`

다음과 같이 작성한다.

```javascript
describe("App.ClickCounter", () => {
  describe("getValue()", () => {
    it("초기값이 0인 카운터 값을 반환한다", () => {
      const counter = App.ClickCounter();
      expect(counter.getValue()).toBe(0);
    });
  });
});
```

![image-20210626182158993](../AppData/Roaming/Typora/typora-user-images/image-20210626182158993.png)

당연하게도 빨간색과 함께 에러가 발생한다. 



`ClickCounter.js`

```javascript
var App = App || {};

App.ClickCounter = () => {
  return {
    getValue: () => 0,
  };
};

```

![image-20210626182621497](../AppData/Roaming/Typora/typora-user-images/image-20210626182621497.png)

초록색과 함께 테스트에 통과했다는 사실을 확인할 수 있다.

하지만 이러한 방법은 옳지 않은 방법이며, 수정을 통해 원하는 기능으로 동작하도록 만든다.



`ClickCounter.js`

```javascript
var App = App || {};

App.ClickCounter = () => {
  let value = 0;
  return {
    getValue: () => value,
  };
};
```

![image-20210626182621499](../AppData/Roaming/Typora/typora-user-images/image-20210626182621497.png)

수정한  코드 역시 테스트에 통과했음을 확인할 수 있다.

하나의 기능에 대해 Red -> Green -> refactor or edit의 방식으로 안전하게 코드를 수정해나갈 수 있다.



이게 바로 테스트 주도 개발이다.



### 두번째 스펙

"ClickCounter 모듈의 increase()는 카운터 값을 1만큼 증가한다."

git checkout -f ClickCounter-spec-2



`ClickCounter.spec.js`

```javascript
describe("App.ClickCounter", () => {
  describe("getValue()", () => {
    it("초기값이 0인 카운터 값을 반환한다", () => {
      const counter = App.ClickCounter();
      expect(counter.getValue()).toBe(0);
    });
  });

  describe("increase()", () => {
    it("카운터를 1 올린다", () => {
      // 준비
      const counter = App.ClickCounter();
      // 실행
      counter.increase();
      // 단언
      expect(counter.getValue()).toBe(1);
    });
  });
});
```

`ClickCounter.js`

```javascript
var App = App || {};

App.ClickCounter = () => {
  let value = 0;

  return {
    getValue() {
      return value;
    },
    increase() {
      return value++;
    },
  };
};
```



성공적으로 테스트 되었지만, 테스트코드에서 중복되는 코드들이 있다.

이러한 코드들을 공통적으로 묶을 수 있는 beforeEach 메서드가 있다.

`ClickCounter.spec.js`

```javascript
describe("App.ClickCounter", () => {
  let counter = null;
  beforeEach(() => {
    counter = App.ClickCounter();
  });
  describe("getValue()", () => {
    it("초기값이 0인 카운터 값을 반환한다", () => {
      expect(counter.getValue()).toBe(0);
    });
  });
  describe("increase()", () => {
    it("카운터를 1 올린다", () => {
      counter.increase();
      expect(counter.getValue()).toBe(1);
    });
  });
});
```

그런데, 카운터 시작이 0이 아니라면 테스트를 통과하지 못할 수 있다.

`ClickCounter.spec.js`

```javascript
describe("App.ClickCounter", () => {
  let counter = null;
  beforeEach(() => {
    counter = App.ClickCounter();
  });
  describe("getValue()", () => {
    it("초기값이 0인 카운터 값을 반환한다", () => {
      expect(counter.getValue()).toBe(0);
    });
  });
  describe("increase()", () => {
    it("카운터를 1 올린다", () => {
      const initialValue = counter.getValue();
      counter.increase();
      expect(counter.getValue()).toBe(initialValue + 1);
    });
  });
});
```

이런식으로 리팩토링 과정을 거칠 수 있다.



**클릭 카운터 뷰 모듈**

카운터 데이터는 돔(DOM)에 반영되어야 한다.

이 역할을 하는 ClickCountView를 구현하자.



**데이터를 주입하자!**

- ClickCounter는 객체를 만들어 파라미터로 전달 받자.
- 데이터를 출력할 DOM Element도 만들어 전달 받자.

TDD 방식으로 사고하다 보면

이런식으로 필요한 모듈을 주입받아 사용하는 경향이 생긴다.



하나의 기능 단위로 모듈을 분리할 수 있기 때문에

단일 책임 원칙을 지킬 수 있다.



먼저 테스트 코드를 작성한다.

`ClickCountView.spec.js`

```javascript
describe("App.ClickCountView", () => {
  let updateEl, clickCounter, view;
  beforeEach(() => {
    clickCounter = App.ClickCounter();
    updateEl = document.createElement("span");
    // 객체 clickCounter와 DOMelement를 파라미터로 전달한다.
    view = App.ClickCountView(clickCounter, updateEl);
  });
  describe("updateView()", () => {
    it("ClickCounter의 getValue() 값을 출력한다", () => {
      const counterValue = clickCounter.getValue();
      view.updateView();
      expect(updateEl.innerHTML).toBe(counterValue.toString());
    });
  });
});
```



`ClickCountView.js`

```javascript
var App = App || {};

// 객체 clickCounter와 DOMelement를 파라미터로 전달한다.
App.ClickCountView = (clickCounter, updateEl) => {
  return {
    updateView() {
      updateEl.innerHTML = clickCounter.getValue();
    },
  };
};
```



잠깐,

ClickCountView에 의존성주입이 제대로 되었는지 어떻게 확인할까?

jasmine은 `toThrowError` 메서드를 통해 에러 여부를 확인할 수 있다.

`ClickCountView.spec.js`

```javascript
describe("App.ClickCountView", () => {
  let updateEl, clickCounter, view;
  beforeEach(() => {
    clickCounter = App.ClickCounter();
    updateEl = document.createElement("span");
    view = App.ClickCountView(clickCounter, updateEl);
  });

  it("clickCounter를 주입하지 않으면 에러를 던진다.", () => {
    const clickCounter = null;
    const updateEl = document.createElement("span");
    const actual = () => App.ClickCountView(clickCounter, updateEl);
    expect(actual).toThrowError();
  });
  it("updateEl을 주입하지 않으면 에러를 던진다.", () => {
    const clickCounter = App.ClickCounter();
    const updateEl = null;
    const actual = () => App.ClickCountView(clickCounter, updateEl);
    expect(actual).toThrowError();
  });
  describe("updateView()", () => {
    it("ClickCounter의 getValue() 값을 출력한다", () => {
      const counterValue = clickCounter.getValue();
      view.updateView();
      expect(updateEl.innerHTML).toBe(counterValue.toString());
    });
  });
});

```



`ClickCountView.js`

```javascript
var App = App || {};

App.ClickCountView = (clickCounter, updateEl) => {
  if (!clickCounter) throw Error("clickCounter이 주입되지 않았습니다.");
  if (!updateEl) throw Error("updateEl이 주입되지 않았습니다.");
  return {
    updateView() {
      updateEl.innerHTML = clickCounter.getValue();
    },
  };
};

```

![image-20210626214122413](../AppData/Roaming/Typora/typora-user-images/image-20210626214122413.png)

에러를 던지는 테스트도 통과함을 알 수 있다.



### 두번째 스펙

"ClickCounterView 모듈의 increaseAndUpdateView()는 카운트 값을 증가하고 그 값을 출력한다."



이 문장은 두개의 기능이 합쳐져있다.

- ClickCounter의 increase 함수를 실행한다.
- updateView를 실행한다.



**테스트 더블**

단위 테스트 패턴으로,

테스트하기 곤란한 컴포넌트를 대체하여 테스트하는 것.

특정한 동작을 흉내낼 뿐이지만 테스트 하기에 적합하다.



다음 5가지가 있다.

더미(dummy) : 인자를 채우기 위해 사용.

스텁(sturb): 더미를 개선하여 실제 동작하게끔 만든 것. 리턴값을 하드 코딩

스파이(spy): 스텁과 유사. 내부적으로 기록을 남기는 추가기능.

페이크(fake): 스텁에서 발전한 코드 운영에서는 사용 불가

목(mock): 더미, 스텁 스파이를 혼합한 형태



jasmine에서는 테스트 더블을 스파이스(spices)라고 부른다.

spyOn(), createSpy() 함수를 사용할 수 있다.



clickCounter 모듈의 increase 함수를 감시하도록 설정한다.

```javascript
spyOn(MyApp, 'foo')
// 특정 행동을 한 뒤,
bar()
//감시한 함수가 실행되었는지 체크한다.
expect(MyApp.foo).toHaveBeenCalled()
```

즉, bar() 함수가 MyApp.foo() 함수를 실행하는지 검증하는 코드다.



`ClickCountView.spec.js`

```javascript
describe("App.ClickCountView 모듈", () => {
  let udpateEl, clickCounter, view;

  it("ClickCounter를 주입하지 않으면 에러를 던진다", () => {
    const clickCounter = null;
    const updateEl = document.createElement("span");
    const actual = () => App.ClickCountView(clickCounter, updateEl);
    expect(actual).toThrowError(App.ClickCountView.messages.noClickCounter);
  });

  it("updateEl를 주입하지 않으면 에러를 던진다", () => {
    const clickCounter = App.ClickCounter();
    const updateEl = null;
    const actual = () => App.ClickCountView(clickCounter, updateEl);
    expect(actual).toThrowError(App.ClickCountView.messages.noUpdateEl);
  });

  beforeEach(() => {
    updateEl = document.createElement("span");
    clickCounter = App.ClickCounter();
    view = App.ClickCountView(clickCounter, updateEl);
  });

  describe("updateView()", () => {
    it("ClickCounter의 getValue() 실행결과를 출력한다", () => {
      const counterValue = clickCounter.getValue();
      view.updateView();
      expect(updateEl.innerHTML).toBe(counterValue.toString());
    });
  });

  describe("increaseAndUpdateView()는", () => {
    it("ClickCounter의 increase 를 실행한다", () => {
      spyOn(clickCounter, "increase");
      view.increaseAndUpdateView();
      expect(clickCounter.increase).toHaveBeenCalled();
    });

    it("updateView를 실행한다", () => {
      spyOn(view, "updateView");
      view.increaseAndUpdateView();
      expect(view.updateView).toHaveBeenCalled();
    });
  });
});
```



`ClickCountView.js`

```javascript
var App = App || {};

App.ClickCountView = (clickCounter, updateEl) => {
  if (!clickCounter) throw new Error(App.ClickCountView.messages.noClickCounter);
  if (!updateEl) throw new Error(App.ClickCountView.messages.noUpdateEl);

  return {
    updateView() {
      updateEl.innerHTML = clickCounter.getValue();
    },
    increaseAndUpdateView() {
      clickCounter.increse();
      this.updateView();
    },
  };
};

App.ClickCountView.messages = {
  noClickCounter: "clickCount를 주입해야 합니다",
  noUpdateEl: "updateEl를 주입해야 합니다",
};
```



### 세번째 스펙

"클릭 이벤트가 발생하면 increaseAndUpdateView() 함수가 실행된다."



카운터 값을 출력할 돔 엘레먼트(updateEl)을 주입했듯이 

클릭 이벤트 핸들러(increaseAndUpdateView)를 바인딩할 돔 엘리멘트(triggerEl)를 주입받자.



먼저 테스트 코드를 작성하자

`ClickCountView.spec.js`

```javascript
describe("App.ClickCountView 모듈", () => {
  let updateEl, clickCounter, view, triggerEl;
  // triggerEl => ClickCountView에 주입할 버튼을 정의한다.  
  beforeEach(() => {
    updateEl = document.createElement("span");
    triggerEl = document.createElement("button");
    clickCounter = App.ClickCounter();
    view = App.ClickCountView(clickCounter, { updateEl, triggerEl });
  });
  describe("네거티브 테스트", () => {
    it("ClickCounter를 주입하지 않으면 에러를 던진다", () => {
      // 주입되는 돔 객체들은 오브젝트 형태로 들어갈 예정.
      const actual = () => App.ClickCountView(null, { updateEl });
      expect(actual).toThrowError(App.ClickCountView.messages.noClickCounter);
    });

    it("updateEl를 주입하지 않으면 에러를 던진다", () => {
      const actual = () => App.ClickCountView(clickCounter, { triggerEl });
      expect(actual).toThrowError(App.ClickCountView.messages.noUpdateEl);
    });
  });

  describe("updateView()", () => {
    it("ClickCounter의 getValue() 실행결과를 출력한다", () => {
      const counterValue = clickCounter.getValue();
      view.updateView();
      expect(updateEl.innerHTML).toBe(counterValue.toString());
    });
  });

  describe("increaseAndUpdateView()는", () => {
    it("ClickCounter의 increase 를 실행한다", () => {
      spyOn(clickCounter, "increase");
      view.increaseAndUpdateView();
      expect(clickCounter.increase).toHaveBeenCalled();
    });

    it("updateView를 실행한다", () => {
      spyOn(view, "updateView");
      view.increaseAndUpdateView();
      expect(view.updateView).toHaveBeenCalled();
    });
    // triggerEl에 클릭 이벤트를 발생시켜 테스트한다.
    it("클릭 이벤트가 발생하면 increseAndUpdateView를 실행한다", () => {
      spyOn(view, "increaseAndUpdateView");
      triggerEl.click();
      expect(view.increaseAndUpdateView).toHaveBeenCalled();
    });
  });
});
```



이전과 테스트 방식은 같지만, 변화를 준 부분은 clickCountView로 주입되는 인자가 많아지기 때문에 object 형식으로 주입하는 것으로 변경되었다.

​	

`ClickCountView.js`

```javascript
var App = App || {};

App.ClickCountView = (clickCounter, { updateEl, triggerEl }) => {
  if (!clickCounter) throw new Error(App.ClickCountView.messages.noClickCounter);
  if (!updateEl) throw new Error(App.ClickCountView.messages.noUpdateEl);
  // return triggerEl에 클릭 이벤트에 increaseAndUpdateView() 함수를 연결하기 위해 view 부분을 위로 끌어올려 정의하였다.
  const view = {
    updateView() {
      updateEl.innerHTML = clickCounter.getValue();
    },
    increaseAndUpdateView() {
      clickCounter.increase();
      this.updateView();
    },
  };
  triggerEl.addEventListener("click", () => view.increaseAndUpdateView());
  return view;
};

App.ClickCountView.messages = {
  noClickCounter: "clickCount를 주입해야 합니다",
  noUpdateEl: "updateEl를 주입해야 합니다",
};
```



![image-20210627153408204](../AppData/Roaming/Typora/typora-user-images/image-20210627153408204.png)

직접 UI를 조작하지 않고 오로지 테스트 코드로 모듈을 구현하고 이를 테스트할 수 있다는 점이 매우 매력적이었다.



화면에 붙여보자.

`index.html`

```html
<html>
  <body>
    <span id="counter-display"></span>
    <button id="btn-increase">Increase</button>

    <script src="ClickCounter.js"></script>
    <script src="ClickCountView.js"></script>

    <script>
      (() => {
        // todo 
        const clickCounter = App.ClickCounter();
        const updateEl = document.querySelector('#counter-display');
        const triggerEl = document.querySelector('#btn-increase');
        const view = App.ClickCountView(clickCounter, { updateEl, triggerEl });
        view.updateView();
      })()
    </script>
  </body>
</html>
```

![image-20210627154244038](../AppData/Roaming/Typora/typora-user-images/image-20210627154244038.png)

잘 동작하는 것을 확인할 수 있다.



기존 코드와 비교

```html
//버튼이 하는일이 매우 많았다. 출력도 하고 값도 변경시키고...
<button onclick="counter++; countDisplay()">증가</button>
<span id="counter-display">0</span>

<script>
  // 전역변수로 선언되었기때문에 꼬일 위험이 있다.
  var counter = 0;
  // 재사용할 수 없다는 점이 문제.
  function countDisplay() {
    document.getElementById('counter-display').innerHTML = counter;
  }
</script>
```



**요구사항**

클릭할때마다 카운터가 올라가네요

그럼 감소 버튼도 추가해주실 수 있나요?

그리고 한 번 클릭하면 2씩 증가하는 기능이 필요할지도 몰라요.



카운터가 여러개 필요한건가?

필요할지도 모른다는건 뭐지?

지금 코드에서는 모듈을 바꿔야하는데...



이러한 요구도 수용할 수 있도록 리팩토링 해보자.



### 세번째 스펙

"ClickCounter 모듈은 '데이터'를 주입 받는다."



`ClickCounter.spec.js`

```javascript
describe("App.ClickCounter", () => {
  let counter;
  let data;
  // 초기 데이터 여부 체크
  it("초기값을 주입하지 않으면 에러를 던진다", () => {
    const actual = () => App.ClickCounter(null);
    expect(actual).toThrowError(App.ClickCounter.messages.noData);
  });
  // 초기값 data 정의
  beforeEach(() => {
    data = { value: 0 };
    counter = App.ClickCounter(data);
  });

  describe("getValue()", () => {
    it("초기값이 0인 카운터 값을 반환한다", () => {
      expect(counter.getValue()).toBe(0);
    });
  });

  describe("increase()", () => {
    it("카운터를 1 올린다", () => {
      const initialValue = counter.getValue();
      counter.increase();
      expect(counter.getValue()).toBe(initialValue + 1);
    });
  });
});
```



`ClickCounter.js`

```javascript
var App = App || {};
// data를 받아 초기값을 정의한다.
App.ClickCounter = (_data) => {
  if (!_data) throw new Error(App.ClickCounter.messages.noData);
  const data = _data;
  data.value = data.value || 0;
  return {
    getValue() {
      return data.value;
    },

    increase() {
      data.value++;
    },
  };
};

App.ClickCounter.messages = {
  noData: "data를 주입해야 합니다",
};
```



data를 넘겨줌으로써 하나의 data에서 value를 관리할 수 있게 되었다.



### 네번째  스펙

"ClickCounter 모듈의 increase 함수는 대체될 수 있다."



값을 올릴수도(increase) 있지만 내릴수도 있다.

함수명 : increase -> counter



`ClickCounter.spec.js`

```javascript
describe("App.ClickCounter", () => {
  let counter;
  const data = { value: 0 };
    
  beforeEach(() => (counter = App.ClickCounter(data)));
    
  describe("setCountFn()", () => {
    it("인자로 함수를 넘기면 count()를 대체한다", () => {
      // 함수 준비
      const add2 = (value) => value + 2;
      const expected = add2(data.value);
      // counter에게 전달
      counter.setCountFn(add2).count();
      const actual = counter.getValue();
      // 동작 확인
      expect(actual).toBe(expected);
    });
  });
});
```

인자로 함수를 넘겨서 그 결과값을 테스트 해본다.



`ClickCounter.js`

```javascript
var App = App || {};

App.ClickCounter = (_data) => {
  if (!_data) throw Error("_data");
  const data = _data;
  data.value = data.value || 0;

  return {
    getValue() {
      return data.value;
    },

    count() {
      data.value++;
    },
    setCountFn(fn) {
      // 인자로 함수를 받아 계산.
      this.count = () => (data.value = fn(data.value));
      // 함수 체이닝을 위해 this를 return
      return this;
    },
  };
};
```



![image-20210629222242004](../AppData/Roaming/Typora/typora-user-images/image-20210629222242004.png)



직접 붙여보자!



```html
<html>
  <body>
    <button id="btn-desc">-</button>
    <span id="counter-display"></span>
    <button id="btn-inc">+</button>

    <script src="ClickCounter.js"></script>
    <script src="ClickCountView.js"></script>

    <script>
      (() => {
         // data 선언
        const data = { value : 0 };
        // ClickCounter 초기화 및 전달함수 구현
        const counterDesc = App.ClickCounter(data).setCountFn(v => v - 1)
        const counterInc = App.ClickCounter(data)

        const clickCounter = App.ClickCounter(data)
        const updateEl = document.querySelector('#counter-display')
        const btnDesc = document.querySelector('#btn-desc')
        const btnInc = document.querySelector('#btn-inc')

        const descCounterView = App.ClickCountView(counterDesc, { updateEl, triggerEl: btnDesc })
        const incCounterView = App.ClickCountView(counterInc, { updateEl, triggerEl: btnInc })
        incCounterView.updateView();

      })()
    </script>
  </body>
</html>
```



자바스크립트 개발하는 독특한 마음가짐을 가져야 한다.

컴파일러가 없는 한 테스트가 최선

테스트주도개발(TDD)

SOLID하고 DRY한 코드 (Counter, CounterView)

견고한 애플리케이션을 만들 수 있다!
