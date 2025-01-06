# 들어가면서
프레임워크 없이 SPA 구현하기, Virtual DOM 구현하기에 이어서 과연 이번엔 또 뭘 만들어보려나... 했더니 
아니나 다를까 또 엄청난 과제가 짠 하고 튀어나왔다. 

바로 **React의 핵심 기능인 Hook과 HOC를 직접 구현**해보는 거였다.

![](https://velog.velcdn.com/images/khy2106/post/690e0632-2fca-4d64-98eb-e0714bcf8215/image.png)

- `shallowEquals`, `deepEquals` 함수 만들기
- HOC (High Order Component) - `memo`, `deepmemo` 만들기
- React Hooks - `useRef`, `useMemo`, `useDeepMemo`, `useCallback` 만들기


학습 메이트님이 이번 과제는 지난 과제들보다 난이도가 낮을 거라고, 잠깐 쉬어가는 타임이 될 것 같다고 했는데, 과제 목록을 보자마자 또 눈앞이 깜깜해졌다. 😵

특히 요즘 개인적으로 진행하는 일까지 겹쳐서, 평일에는 3주 연속으로 새벽 2~3시에 취침하고 있다. 물론 과제 제출일인 금요일은 제외하고.

처음 한 결심과는 다르게 생활 패턴이 흔들리니 마음이 약해져가는 걸 느꼈다._ 나는 뭘 믿고 이걸 하겠다고 한 걸까? 그냥 회사만 다녀도 내 몸 하나도 건사하기 힘든데._

하지만 내가 항해 플러스 과제를 하면서 깨달은 게 있다. '_**할 때는 죽을 것 같지만, 끝내고 나면 왜 했는지 알 것 같다**_'는 거. 이 사실을 떠올리며 이번 과제도 열심히 해보기로 마음먹었다.


# 3주차 멘토링 후기
![](https://velog.velcdn.com/images/khy2106/post/8d67561a-e4ef-4224-8471-5cd1b3831131/image.png)

이번 주는 현재 육아 휴직 중이신 **뱅크샐러드의 김나영 코치**님으로부터 멘토링을 받았다. 
이날은 뭐가 급했는지 zep도 부랴부랴 접속하는 바람에 화면 캡처도 못 했다. 

나영 코치님은 실용주의적인 스타일이신 것 같았다._ MBTI로 치면 아마도 xSTx 유형이지 않을까? 조심스럽게 추측해 본다._ 팀원들이 남긴 질문에 간결하면서도 명확하게 답변해 주셔서 집중하는 데 도움이 됐다.

게다가 이번에 내가 꽤 개인적인 질문을 드렸는데도 친절하게 답변해 주셨고, 더 나아가 그 내용을 잘 아시는 다른 코치님께까지 내 이야기를 전달해주시는 세심함을 보여주셨다.

코치님의 블로그에서 회고록을 읽어보니 더 놀라웠다. 출산한 지 얼마 되지 않으셨는데도 주니어 개발자를 대상으로 한 발표까지 하셨다니 정말 존경스러웠다. 

여러 가지 이유로 임신/출산을 미루고 있는 나로서는 회고록에 있는 글들이 하나하나 깊게 와닿았다. **_'언젠간 나도 아이를 가질 수 있을까? 출산하고 나서도 개발자로서의 커리어를 이어갈 수 있을까?'_** 하는 고민들에 대해, 나영 코치님의 행적은 나에게 큰 희망이 되었다.


# 3주차 과제 WIL 
## 비교 함수 만들기 - shallowEquals(얕은 비교), deepEquals(깊은 비교) 
### 원시 타입과 참조 타입

먼저 JavaScript의 데이터 타입에는 크게 두 가지가 있다.

**1. 원시 타입(primitive type)
**
> 원시 타입
> Number, String, Boolean, Symbol, BigInt, undefined, null

- 데이터가 변수에 직접 저장됨
- 변수를 복사할 때 값 자체가 복사됨
- 값 자체를 비교하므로, 얕은 비교, 깊은 비교를 따질 필요 없음.

**2. 참조 타입(reference type)
**
> 참조 타입
> Object, Array, Function 등

- **데이터는 메모리에 저장**되고, 변수에는 그 주소가 저장됨
- 변수를 복사할 때 **메모리 주소만 복사됨**
- 참조값을 비교하는지, 실제 값을 비교하는지에 따라 얕은 비교와 깊은 비교로 나뉨.


### 비교란 무엇일까? 얕은 비교, 깊은 비교

비교: 메모리에 저장되는 참조 타입(Object, Array 등)의 값이나 구조가 서로 같은지 비교할 때 쓰는 개념

#### 1. 얕은 비교(Shallow Equals) 
**객체의 최상위 속성들만 비교하며, 중첩된 객체는 참조값(메모리 주소)만 비교함**

- 객체의 최상위 속성들만 비교
- 중첩된 객체는 참조값(메모리 주소)만 비교
- Object.is() 또는 === 연산자로 구현

```ts
import { DependencyList } from "react";

// Comparable: 비교 가능한 데이터 타입으로, React의 DependencyList(불변 배열) 또는 객체를 허용
type Comparable = DependencyList | Record<string, unknown>;

export function shallowEquals(
  objA: Comparable,
  objB: Comparable
): boolean {
  // 1. 빠른 참조 동등성 비교 (참조값이 같다면 동일한 객체/배열임)
  if (objA === objB) {
    return true;
  }

  // 2. 한쪽이 null이거나 타입이 다르면 비교 불가
  if (
    objA === null || 
    objB === null || 
    typeof objA !== typeof objB
  ) {
    return false;
  }

  // 3. 두 값이 배열인지 확인하고, 배열일 경우 shallow comparison
  if (Array.isArray(objA) && Array.isArray(objB)) {
    // 배열의 길이가 다르면 false
    if (objA.length !== objB.length) {
      return false;
    }

    // 배열의 각 요소를 비교 (===를 사용해 얕은 비교)
    return !objA.some((item, index) => item !== objB[index]);
  }

  // 4. 배열이 아닌 경우, 두 값이 객체라고 가정하고 shallow comparison
  if (!Array.isArray(objA) && !Array.isArray(objB)) {
    const keysA = Object.keys(objA); // 첫 번째 객체의 키 배열
    const keysB = Object.keys(objB); // 두 번째 객체의 키 배열

    // 키의 개수가 다르면 객체는 다르다고 판단
    if (keysA.length !== keysB.length) {
      return false;
    }

    // 각 키에 대해 값이 동일한지 확인 (얕은 비교)
    return keysA.every(
      (key) => objA[key] === objB[key]
    );
  }

  // 5. 한쪽이 배열이고 다른 쪽이 객체라면 다르다고 판단
  return false;
}

```


#### 2. 깊은 비교(Deep Equals)
**객체의 모든 속성을 재귀적으로 비교하며, 중첩된 객체의 실제 값까지 비교함.**
- 객체의 모든 depth의 값을 재귀적으로 비교
- 중첩된 객체들의 실제 값까지 일일이 비교
- 주소값이 달라도 모든 값이 같으면 같다고 판단

```ts
export function deepEquals<T>(objA: T, objB: T): boolean {
  // 1. 참조가 같은 경우
  if (objA === objB) {
    return true;
  }

  // 2. null 또는 undefined 처리
  if (objA == null || objB == null) {
    return objA === objB;
  }

  // 3. 타입이 다른 경우
  if (typeof objA !== typeof objB) {
    return false;
  }

  // 4. 배열 처리
  if (Array.isArray(objA) && Array.isArray(objB)) {
    if (objA.length !== objB.length) {
      return false;
    }

    return objA.every((value, index) => deepEquals(value, objB[index]));
  }

  // 5. 일반 객체 처리
  if (typeof objA === "object" && typeof objB === "object") {
    const keysA = Object.keys(objA) as (keyof T)[];
    const keysB = Object.keys(objB) as (keyof T)[];

    // 키 개수 비교
    if (keysA.length !== keysB.length) {
      return false;
    }

    // 모든 키와 값을 재귀적으로 비교
    return keysA.every(
      (key) =>
        Object.prototype.hasOwnProperty.call(objB, key) &&
        deepEquals(objA[key], objB[key]),
    );
  }

  // 6. 나머지 경우
  return false;
}
```

| **특징**           | **얕은 비교(Shallow Equals)**               | **깊은 비교(Deep Equals)**                   |
|---------------------|--------------------------------------------|---------------------------------------------|
| **비교 범위**       | 객체의 최상위 속성만 비교                   | 객체의 모든 속성과 중첩된 데이터까지 비교      |
| **중첩된 객체**     | 참조값만 비교                               | 중첩된 데이터의 실제 값까지 비교              |
| **속도**           | 빠름                                       | 느릴 수 있음                                 |
| **구현 방법**       | `Object.is()` 또는 `===` 사용              | 재귀 함수, 전용 라이브러리 (예: Lodash `isEqual`) 사용 |



### 사용 예시

**1. 얕은 비교**

```ts
// 배열 비교
const arr1 = [1, 2, 3];
const arr2 = [1, 2, 3];
const arr3 = [1, 2, 4];

console.log(shallowEquals(arr1, arr2)); // true
console.log(shallowEquals(arr1, arr3)); // false

// 객체 비교
const obj1 = { name: "Kim", age: 25 };
const obj2 = { name: "Kim", age: 25 };
const obj3 = { name: "Lee", age: 25 };

console.log(shallowEquals(obj1, obj2)); // true
console.log(shallowEquals(obj1, obj3)); // false

// 중첩된 객체/배열의 경우
const nested1 = { user: { name: "Kim" } };
const nested2 = { user: { name: "Kim" } };
console.log(shallowEquals(nested1, nested2)); // false (얕은 비교이므로)

// React deps에서의 활용
useEffect(() => {
  // 효과 실행
}, [obj1]); // obj1이 얕은 비교로 변경되었을 때만 실행
```

**2. 깊은 비교 **

```ts
// 기본 타입 비교
console.log(deepEquals(1, 1)); // true
console.log(deepEquals("hello", "hello")); // true
console.log(deepEquals(true, false)); // false

// 중첩 객체 비교
const obj1 = {
 name: "Kim",
 info: {
   age: 25,
   hobbies: ["reading", "gaming"]
 }
};

const obj2 = {
 name: "Kim", 
 info: {
   age: 25,
   hobbies: ["reading", "gaming"]
 }
};

const obj3 = {
 name: "Kim",
 info: {
   age: 25,
   hobbies: ["reading", "sports"]
 }
};

console.log(deepEquals(obj1, obj2)); // true
console.log(deepEquals(obj1, obj3)); // false

// 중첩 배열 비교 
const arr1 = [1, [2, 3], {x: 4}];
const arr2 = [1, [2, 3], {x: 4}];
const arr3 = [1, [2, 3], {x: 5}];

console.log(deepEquals(arr1, arr2)); // true 
console.log(deepEquals(arr1, arr3)); // false
```

## HOC 만들기 - memo, deepmemo
### HOC란 무엇일까?

>HOC(Higher Order Component)는 컴포넌트를 인자로 받아 새로운 컴포넌트를 반환하는 함수이다.

보통 함수 인자에는 string, number, function(콜백함수)를 넘겨줘야 할 것 같지만 아예 컴포넌트 자체를 인자로 넘기는 것을 뜻한다. 정리하자면, 컴포넌트를 일반 데이터처럼 인자로 넘기고 반환하는 함수.

### memo 함수
- 불필요한 리렌더링을 방지
- 동일한 props에 대해 이전 렌더링 결과를 재사용
- 컴포넌트의 성능 향상

```ts
import { createElement, ReactNode } from "react";
import { shallowEquals } from "../equalities";
import { useRef } from "../hooks";

/**
 * 동작 방식:
 * 1. 이전 props와 현재 props를 비교
 * 2. props가 변경되지 않았다면 이전에 렌더링된 결과를 재사용
 * 3. props가 변경되었다면 새로 렌더링
 */
export function memo<P extends object>(
    Component: React.FC<P>,
    equals = shallowEquals,
): React.FC<P> {
    // 메모이제이션된 컴포넌트 정의
    const MemoizedComponent: React.FC<P> = (props) => {
        // 이전 props를 저장하기 위한 ref
        const prevPropsRef = useRef<P | null>(null);
        // 이전 렌더링 결과를 저장하기 위한 ref
        const renderedRef = useRef<ReactNode>(null);

        // 다음 조건에서 새로 렌더링:
        // 1. 첫 렌더링인 경우 (prevPropsRef.current === null)
        // 2. props가 변경된 경우 (!equals(prevPropsRef.current, props))
        // 3. 이전 렌더링 결과가 없는 경우 (renderedRef.current === null)
        if (
            prevPropsRef.current === null ||
            !equals(prevPropsRef.current, props) ||
            renderedRef.current === null
        ) {
            // 현재 props 저장
            prevPropsRef.current = props;
            // 새로운 렌더링 결과 생성 및 저장
            renderedRef.current = createElement(Component, props);
        }

        // 저장된 렌더링 결과 반환
        return renderedRef.current as ReactNode;
    };

    return MemoizedComponent;
}
```
- 이전 props와 현재 props를 비교
- props가 같으면 → 이전 렌더링 결과 재사용
- props가 다르면 → 새로 렌더링 수행

### deepMemo 함수
- 객체나 배열같은 깊은 구조를 가진 props를 정확하게 비교
- 중첩된 데이터 구조에서도 정확한 메모이제이션 보장

```ts
import { deepEquals } from "../equalities";
import { memo } from "./memo.ts";

export function deepMemo(Component: React.FC) {
  return memo(Component, deepEquals);
}
```

### 사용 예시
```ts
// memo 사용 예시
const SimpleComponent = ({ name, age }: { name: string, age: number }) => (
 <div>{name}: {age}</div>
);

const MemoizedSimple = memo(SimpleComponent);

// props가 단순한 값일 때 효과적
<MemoizedSimple name="John" age={25} /> // 재사용

// deepMemo 사용 예시 
const ComplexComponent = ({ user }: { user: { details: { name: string, settings: any[] }}}) => (
 <div>{user.details.name}</div>
);

const MemoizedComplex = deepMemo(ComplexComponent);

// 중첩된 객체의 변경 감지
<MemoizedComplex user={{
 details: {
   name: "John",
   settings: [{theme: "dark"}]
 }
}} />
```


## React Hook 
### Hook이 무엇일까?

> Hook이란? React 함수형 컴포넌트에서 상태 관리(state management)와 생명주기 이벤트(lifecycle events)를 쉽게 다룰 수 있도록 제공되는 함수

- 클래스 컴포넌트에서의 this.state나 생명주기 메서드(componentDidMount, componentDidUpdate, componentWillUnmount) 같은 기능을 대체하거나 보완하기 위해 도입됨.
- 단순히 생명주기만 처리하는 것이 아니라, 상태 관리, 참조값 유지, 부수효과 처리 등 다양한 기능을 제공한다.


### useRef
- DOM 요소를 직접 참조. 렌더링 간 값이 유지되는 "불변 상태" 저장.

useState를 이용해 구현한 useRef
```ts
export function useRef<T>(initialValue: T): { current: T } {
  const [ref] = useState(() => ({ current: initialValue })); 

  return ref; 
}
```
- useState는 상태가 변경될 때마다 컴포넌트를 리렌더링한다.
- useState의 초기화 함수는 최초 1회만 실행된다.
- 따라서 { current: initialValue } 객체는 한 번만 생성되고, 이후엔 동일한 참조가 유지된다.
- current 값을 변경해도 리렌더링이 발생하지 않는다. 객체 자체는 변하지 않기 때문이다.

### useMemo
- 계산 비용이 큰 값의 재계산 방지
- 불필요한 리렌더링 최적화

```ts
import { DependencyList, useRef } from "react";

export function useMemo<T>(
  factory: () => T,
  deps: DependencyList,
  equals: (a: DependencyList, b: DependencyList) => boolean = (a, b) =>
    a.length === b.length && a.every((v, i) => v === b[i]),
): T {
  // 이전 의존성 저장
  const previousDepsRef = useRef<DependencyList | null>(null);
  // 메모이제이션된 값 저장
  const memoizedValueRef = useRef<T | null>(null);

  // 의존성 변경 감지
  const dependenciesChanged =
    !previousDepsRef.current || !equals(previousDepsRef.current, deps);

  if (dependenciesChanged) {
    // 의존성이 변경되었을 경우 재계산
    memoizedValueRef.current = factory();
    previousDepsRef.current = deps;
  }

  return memoizedValueRef.current as T;
}
```
- useRef로 이전 의존성과 메모이제이션 값 저장
- deps 배열이 변경될 때만 factory 함수 실행
- 의존성 변경 감지 로직으로 메모이제이션 제어

### useCallback
-  의존성 배열이 변경될 때만 새로운 함수 인스턴스를 생성
- 자식 컴포넌트에 콜백을 props로 전달할 때 리렌더링 최적화
- useEffect의 의존성으로 함수를 사용할 때 불필요한 실행 방지

```ts
import { DependencyList } from "react";
import { useMemo } from "./useMemo";

export function useCallback<
  T extends (...args: Parameters<T>) => ReturnType<T>,
>(callback: T, deps: DependencyList): T {
  return useMemo(() => callback, deps);
}

```
- 위에 정의한 useMemo를 이용해 구현 (callback 함수와 deps를 useMemo에 인자로 넘긴다.)

# 마무리하면서
갑분타(갑자기 분위기 타입)이긴 하지만, 타입스크립트의 **DependencyList, Parameters, ReturnType** 같은 새로운 타입들을 이번에 처음 접했다. 사실 과제를 진행하면서 동작 구현에만 집중하다 보니 타입 정의 부분은 좀 허술하게 넘어간 느낌이다. 각 함수가 어디서 어떻게 쓰이는지 먼저 이해하고 접근했다면 타입도 자연스럽게 이해됐을 텐데, 그게 좀 아쉽다.

근데 항상 그렇듯 항해 과제는 끝나고 나서야 그 가치를 알게 된다. React의 HOC와 Hook을 직접 구현해보니까 이게 어떤 원리로 돌아가는지가 눈에 보이더라. 특히 useCallback이 useMemo로, useMemo는 useRef로, useRef는 useState로 구현되는 걸 보면서 결국 **모든 복잡한 기능도 기본적인 함수들의 조합** 이라는 걸 실감했다.

**React의 핵심 최적화 기능들도 단순 비교 로직에서 시작된다**는 게 흥미로웠다. _memo는 그저 이전 props와 현재 props를 비교하는 거고, deepMemo는 여기에 깊은 비교 로직만 더한 거잖아!_ 이런 식으로 복잡해 보이는 기능들도 결국은 단순한 기본기의 조합이라는 걸 보면서, 기본기의 중요성을 다시 한 번 느꼈다.

앞으로 라이브러리나 프레임워크를 사용할 때도 '**이게 어떤 원리로 동작하는 거지?**'라는 호기심을 가지고 접근해봐야겠다. 그리고 새로운 기능을 만들 때도 복잡하게 생각하기보다는, 이미 있는 단순한 기능들을 어떻게 조합할 수 있을지 고민해보는 습관을 들여야겠다는 생각이 든다.

![](https://velog.velcdn.com/images/khy2106/post/23026a1f-94b8-4f6e-bcb1-c88ab0294215/image.png)

이번 주차도 고생 많았다 :) 
