# 클래스형 컴포넌트와 함수형 컴포넌트

![클래스형 컴포넌트와 함수형 컴포넌트의 차이](https://velog.velcdn.com/images/clydehan/post/e0d78a70-a458-47b5-98db-d82e08640d93/image.png)

리액트(React)에서는 컴포넌트를 정의하는 두 가지 주요 방법 클래스형 컴포넌트(Class Components)와 함수형 컴포넌트(Function Components)가 있다. 두 컴포넌트는 비슷한 역할을 하지만, 작성 방식과 몇 가지 중요한 특성에서 차이가 있다.

---

## 클래스형 컴포넌트(Class Components)

클래스형 컴포넌트는 ES6 클래스 문법을 사용하여 정의된다. 리액트의 초기 버전부터 사용된 방식으로, 상태(state)와 생명주기 메서드(lifecycle methods)를 가질 수 있다.

![리액트 컴포넌트의 생명주기](https://velog.velcdn.com/images/clydehan/post/d3a5a0fc-264b-4320-ae02-60d3373a9cef/image.png)

> 💡 리액트 컴포넌트의 **생명주기(lifecycle)** 란, 컴포넌트가 생성되고, 업데이트되고, 소멸되는 과정에서 발생하는 일련의 단계들을 의미한다. 이 생명주기 동안 특정 시점에 특정 작업을 수행할 수 있는 메서드나 훅을 제공하여 개발자가 필요한 처리를 할 수 있도록 한다.

---

### 장점

#### 생명주기 메서드의 명확성

- componentDidMount, componentDidUpdate, componentWillUnmount 등 생명주기 메서드가 명확하게 분리되어 있어, 각 시점에서 수행할 작업을 쉽게 관리할 수 있다.
- 예시: 데이터 로딩, 이벤트 리스너 등록/해제 등

```tsx
class MyClassComponent extends React.Component {
  componentDidMount() {
    console.log("Component did mount");
    // 데이터 로드, 타이머 설정 등
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      console.log("Component did update");
      // 상태가 변경된 후에 실행할 작업
    }
  }

  componentWillUnmount() {
    console.log("Component will unmount");
    // 타이머 정리, 이벤트 리스너 제거 등
  }

  render() {
    return <div>My Class Component</div>;
  }
}
```

#### 타입스크립트와의 호환성

- 클래스형 컴포넌트는 타입스크립트와의 통합이 더 직관적이다. props와 state에 대한 타입을 클래스 속성으로 쉽게 정의할 수 있다.
- 예시: 타입스크립트를 사용한 클래스형 컴포넌트

```tsx
import React, { Component } from "react";

interface MyProps {
  initialCount: number;
}

interface MyState {
  count: number;
}

class MyClassComponent extends Component<MyProps, MyState> {
  constructor(props: MyProps) {
    super(props);
    this.state = {
      count: props.initialCount,
    };
  }

  render() {
    return <div>Count: {this.state.count}</div>;
  }
}
```

#### 초기 리액트 개발자들에게 친숙

- 초기 리액트에서는 클래스형 컴포넌트가 유일한 방법이었기 때문에, 오래된 코드베이스나 기존 프로젝트에서는 여전히 많이 사용된다.

---

### 단점

#### 보일러플레이트 코드

- 클래스형 컴포넌트는 더 많은 초기 설정과 코드가 필요하다. 특히, this 바인딩이 필요할 때 코드가 복잡해질 수 있다.

```tsx
class MyClassComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };

    // this 바인딩
    this.incrementCount = this.incrementCount.bind(this);
  }

  incrementCount() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.incrementCount}>Increment</button>
      </div>
    );
  }
}

export default MyClassComponent;
```

> 💡 **this 바인딩이란?**
> 자바스크립트에서 this 키워드가 가리키는 객체를 특정 함수나 메서드 내에서 명확하게 정의하는 과정을 말한다. 클래스 메서드가 호출될 때 this가 올바르게 가리키지 않으면 예상치 못한 동작이나 에러가 발생할 수 있다.
>
> this 키워드는 자바스크립트에서 함수가 호출되는 방식에 따라 달라지는 특성을 가지고 있다. 따라서 클래스형 컴포넌트의 메서드가 호출될 때 this가 올바른 컨텍스트(컴포넌트 인스턴스)를 가리키도록 보장해야 한다.
>
> 💡 **왜 this 바인딩이 필요한가?**
> 클래스형 컴포넌트의 메서드가 이벤트 핸들러로 전달될 때, 기본적으로 this가 해당 클래스 인스턴스를 가리키지 않을 수 있다. 이를 해결하기 위해 this를 명시적으로 바인딩해야 한다.

#### 상태 관리의 복잡성

- setState 메서드를 통해 상태를 업데이트하는 것이 다소 번거로울 수 있으며, 비동기적 특성 때문에 예측하기 어려운 경우가 발생할 수 있다.

---

## 함수형 컴포넌트(Function Components)

함수형 컴포넌트도 클래스형 컴포넌트와 마찬가지로 상태와 생명주기 기능을 지원하지만, 리액트 훅(Hooks)의 도입으로 인해 함수형 컴포넌트가 이 기능들을 더 간결하고 직관적으로 처리할 수 있게 되었다.

---

### 장점

```tsx
import React, { useState, useEffect } from "react";

const MyFunctionComponent = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Component did mount");
    return () => {
      console.log("Component will unmount");
    };
  }, []);

  useEffect(() => {
    console.log("Component did update");
  }, [count]);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};

export default MyFunctionComponent;
```

#### 간결함

- 함수형 컴포넌트는 코드가 간결하고 직관적이다. 함수형 프로그래밍의 장점을 살려 컴포넌트를 쉽게 작성할 수 있다.

#### 리액트 훅(Hooks) 사용

- useState, useEffect, useContext 등 다양한 훅을 사용하여 상태와 생명주기 로직을 간단하게 관리할 수 있다.

#### 재사용성

- 훅을 사용하면 클래스형 컴포넌트보다 상태 관리 로직을 쉽게 재사용할 수 있다. 예를 들어, 커스텀 훅을 만들어 여러 컴포넌트에서 동일한 로직을 사용할 수 있다.

```tsx
import { useState, useEffect } from "react";

// 커스텀 훅 정의
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return { count, increment, decrement };
}

// 커스텀 훅 사용
const MyComponent = () => {
  const { count, increment, decrement } = useCounter();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
};
```

#### 성능

함수형 컴포넌트는 클래스형 컴포넌트보다 메모리 사용량이 적고, 리액트의 최적화를 더 잘 활용할 수 있다.

---

### 단점

#### 생명주기 메서드의 직관성 부족

- 훅을 처음 사용하는 개발자에게는 useEffect 등 생명주기 훅이 직관적이지 않을 수 있다. 적절한 사용 시점을 파악하는 데 시간이 걸릴 수 있다.
- 예시: useEffect의 복잡한 사용

```tsx
const MyComplexComponent = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const handleResize = () => console.log("Resized");
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <div>Count: {count}</div>;
};
```

#### 복잡한 상태 로직

- 훅을 사용한 복잡한 상태 로직은 코드의 가독성을 떨어뜨릴 수 있다. 특히 여러 훅을 중첩하여 사용하는 경우 코드가 난해해질 수 있다.
- 예시: 여러 훅의 중첩 사용

```tsx
const MyComplexStateComponent = () => {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  useEffect(() => {
    console.log("Count updated:", count);
  }, [count]);

  useEffect(() => {
    console.log("Text updated:", text);
  }, [text]);

  return (
    <div>
      <p>Count: {count}</p>
      <p>Text: {text}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
    </div>
  );
};
```

---

## 차이점

리액트의 현재 트렌드는 주로 함수형 컴포넌트를 사용하는 방향으로 가고 있으며, 새로운 기능들도 주로 함수형 컴포넌트를 중심으로 추가되고 있다. 그러나 특정 상황에서는 여전히 클래스형 컴포넌트를 사용할 수 있으며, 두 방식 모두 장단점이 있다.

### 상태와 생명주기 관리

클래스형 컴포넌트는 this.state와 생명주기 메서드를 사용한다.
함수형 컴포넌트는 리액트 훅을 사용한다. (useState, useEffect 등).

### 코드의 간결함

함수형 컴포넌트는 클래스형 컴포넌트보다 코드가 간결하고 직관적이다.

### 성능

함수형 컴포넌트는 리액트가 내부적으로 최적화를 잘 할 수 있어 일반적으로 성능이 더 좋다.

### 개발자 경험

함수형 컴포넌트는 코드가 더 간단하고 직관적이므로, 특히 리액트 훅을 활용할 때 개발자 경험이 더 좋다.

---

## 함수형 컴포넌트가 더 많이 사용되는 이유

공식문서에서도 클래스형 컴포넌트보다는 함수형 컴포넌트를 권장하고 있다. 그 이유는 뭘까?

> [리액트 공식문서](https://react.dev/reference/react/Component)

### 리액트 훅의 도입

리액트 16.8 버전에서 훅이 도입되면서 함수형 컴포넌트가 상태 관리와 생명주기 메서드를 사용할 수 있게 되었다. 이는 함수형 컴포넌트의 활용 범위를 크게 확장시켰다.

### 간결하고 직관적인 코드

함수형 컴포넌트는 코드가 간결하고 직관적이다. 초기 설정이 적고, this 바인딩 등의 복잡성을 피할 수 있다.

### 재사용성과 테스트 용이성

훅을 사용하면 상태 관리 로직을 재사용할 수 있고, 독립적인 함수로 작성되기 때문에 테스트하기도 용이하다.

### 커뮤니티와 생태계의 지원

리액트 커뮤니티와 많은 라이브러리들이 함수형 컴포넌트를 지원하는 방향으로 발전하고 있다. 공식 문서에서도 함수형 컴포넌트를 기본으로 설명하는 경향이 있다.

---

## 클래스형 컴포넌트의 사용이 줄어드는 이유

### 리액트 훅의 강력함

리액트 훅은 클래스형 컴포넌트의 기능을 대체할 수 있으며, 더 나아가 훨씬 더 간단하고 강력한 방법을 제공한다.

### 복잡성 증가

클래스형 컴포넌트는 초기 설정과 this 바인딩 등의 이유로 코드가 복잡해질 수 있다.

### 최신 기능과의 호환성 부족

리액트의 최신 기능들은 대부분 함수형 컴포넌트를 염두에 두고 개발된다. 따라서 최신 기능들을 활용하기 위해서는 함수형 컴포넌트를 사용하는 것이 더 유리하다.

---

# 정리

리액트 컴포넌트를 작성할 때 클래스형 컴포넌트와 함수형 컴포넌트는 각각의 장단점이 있으며, 상황에 따라 적절히 선택해야 한다. 최근의 트렌드는 함수형 컴포넌트를 중심으로 이루어지고 있으며, 이는 리액트 훅의 강력한 기능과 간결한 코드 작성 방식 때문이다. 기존 코드베이스나 특정 복잡한 생명주기 로직이 필요한 경우에는 여전히 클래스형 컴포넌트를 사용할 수 있다. 하지만 새로운 프로젝트나 간단한 로직의 컴포넌트를 작성할 때는 함수형 컴포넌트를 사용하는 것이 일반적이다.

---

# 참고문헌

> [React Components: Class vs Functional](https://www.linkedin.com/pulse/react-components-class-vs-functional-ali-haider)
> [React Lifecycle - Class Components](https://info.keylimeinteractive.com/react-lifecycle-class-components) > [React 클래스형 컴포넌트에서 함수형 컴포넌트로 바뀐 이유](https://velog.io/@shinyejin0212/React-%ED%81%B4%EB%9E%98%EC%8A%A4%ED%98%95-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%97%90%EC%84%9C-%ED%95%A8%EC%88%98%ED%98%95-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A1%9C-%EB%B0%94%EB%80%90-%EC%9D%B4%EC%9C%A0) > [리액트 공식 문서](https://react.dev/reference/react/Component)
