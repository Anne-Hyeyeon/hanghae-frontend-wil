# 들어가면서

이번 주 과제는 역대급으로 어렵다고 학습메이트님이 여러 번 말씀하셨는데... 그 말은 사실이었다 ㅠㅠ

제목 그대로 **바닐라 자바스크립트로 Virtual DOM을 직접 만들어 보기**! 솔직히 말로만 들었지 그걸 직접 하게 될 줄은 상상도 못 했다.

과제의 의도는 Virtual DOM을 직접 구현해 보면서 리액트의 작동 원리와 렌더링 과정을 깨우치는 거였는데, 

결과적으로는 리액트가 왜 필요한지, Virtual DOM의 정체가 무엇인지 알게 되었다. 

처음에는 막막했던 과제였지만, 이를 통해 프론트엔드 개발의 핵심 개념을 직접 경험하고 이해할 수 있었던 값진 시간이었다.

![](https://velog.velcdn.com/images/khy2106/post/76b3f574-72c8-4851-835a-cca2813cd614/image.png)


# 두 번째 멘토링 후기

저번 주처럼 이번 주차에도 멘토링이 진행되었는데, 우리 조는 테오 코치님으로 예약했다.

>항해 플러스 프론트엔드 과정에서는 1주일에 한 번 멘토링을 받고 싶은 코치님을 선택해, 한 시간 동안 과제와 커리어에 대한 조언을 들을 수 있다 :)

1주차의 오프 코치님 멘토링 때는 개발 공부 방향성과 학습할 때의 태도에 대한 질문을 했었다. 워낙 만족스러웠기에 이번 멘토링도 기대가 되었다.


![](https://velog.velcdn.com/images/khy2106/post/8d2edfd8-b073-4af8-a0e7-1f1c14595031/image.png)

그런데 테오 코치님이 생각보다 더 어마어마한(?) 자료들을 준비해 오셨고, 결국 원래 예정된 멘토링 시간보다 한 시간이나 더 상담을 해 주셨다. 커리어에 대한 깊이 있는 고민부터 이번 주 과제에 대한 조언까지, 내용이 정말 알차서 팀원들의 만족도도 대단했다.

중간에 '이번 주 과제를 통해 알게 된 점'을 작성할 때, 테오 코치님이 팀원 한 명 한 명한테 새롭게 알게 된 사실을 설명하라고 한 적이 있다.

그런데 면접을 보고 발표를 한 지가 너무 오래되어서인지 이미 안다고 생각하는 것조차 설명하기가 힘들었다 (ㅠㅠ). 

<br />

테오 코치님은 팀원 중 한 명과 짝을 지어주면서, 서로 설명하는 연습을 해 오라는 미션까지 내 주셨다.

_**이직을 하지 않는다고 해서 설명하기, 말하기 연습을 게을리하면 안 된다는 생각이 들었다. 
**_
사실, 내용을 제대로 흡수했다면 설명하는 건 그다지 어려운 일이 아닌데 말이다. 핵심은 내가 아직 이것들을 자신 있게 가르쳐줄 정도로 개념을 확실히 파악하지 못했다는 것 같다.

어쨌든, 나 같은 주니어 개발자들은 선배 개발자들의 코칭을 듣기 위해서는 강연에 참석하거나 개발자 컨퍼런스에 가야 한다. 그마저도 일대 다 형식의 발표 위주라 개인적으로 상담을 받을 기회는 제한되어 있다. 

하지만 항해 플러스 프론트엔드 과정에서는** 1주일에 한 번씩 실력자 개발자 선배한테, 그것도 개인적인 고민에 대해서 상담을 받을 수 있다는 점**. 솔직히 말하면 이것만으로도 항해 플러스 과정을 들을 가치는 충분하지 않은가 싶다.

![](https://velog.velcdn.com/images/khy2106/post/578721e2-d493-4267-8d6a-fc28bafd8f27/image.png)


# 2주차 과제 WIL

## Virtual DOM 이해하기

Virtual DOM을 바닐라 자바스크립트로 구현하기 위해서는 뭘 해야 된다? 일단 가상 돔이 무엇인지 알아야 한다.

- Virtual DOM이 무엇인가?
> Virtual DOM은 실제 DOM의 가벼운 복사본이다.

- 그럼 DOM은 뭔데?
> DOM이란, 웹 페이지를 객체를 계층구조로 표현한 일종의 설계도이다. 자바스크립트는 DOM을 조작해 브라우저에게 변경사항을 알리고, 브라우저는 이를 기반으로 실제 화면을 렌더링한다.

- 그렇다면 도대체 왜 DOM의 복사본이 필요한 걸까?
> DOM 조작에는 큰 비용이 들기 때문이다. DOM이 변경될 때마다 브라우저는 레이아웃을 다시 계산하고(reflow) 화면을 다시 그리는(repaint) 과정을 거쳐야 하기 때문에, 잦은 DOM 조작은 성능 저하를 일으킨다.

- DOM 복사본인 Virtual DOM은 어떤 원리로 작동하는 걸까?
> 1. 상태 변경이 일어나면 우선 Virtual DOM에 새로운 UI를 렌더링한다 (메모리에서만 발생)
>2. 이전 Virtual DOM과 새로운 Virtual DOM을 비교한다 (diffing)
>3. 실제로 바뀐 부분만 찾아낸다
>4. 바뀐 부분만 실제 DOM에 적용한다 (reconciliation)

## 바닐라 자바스크립트로 Vitual DOM을 만들기 위해서는 어떤 함수들이 필요할까?

그 다음으로는 Virtual DOM을 만들기 위한 과정과 필요한 함수에 대한 이해가 필요하다. 
### createVNode 함수

>Virtual DOM의 노드를 생성하는 기초 함수
> type(태그 이름), props(속성), children(자식 요소들)을 받아서 객체 구조로 만듦

```js
export function createVNode(type, props, ...children) {
  // children 배열을 평탄화하고 정리
  const flattenedChildren = children
    .flat(Infinity)
    .filter((child) => child != null && child !== false && child !== true);

  // props가 null이면 빈 객체로 초기화
  const normalizedProps = props || null;

  // vNode 객체 생성
  return {
    type,
    props: normalizedProps,
    children: flattenedChildren,
  };
}
```
- 노드란? UI를 구성하는 각각의 요소를 나타내는 JS 객체.
예를 들면 이런 HTML이 있다고 할 때 : 
```html
<div class="container">
    <h1>제목</h1>
    <p>내용</p>
</div>
```

- 이것을 Virtual DOM의 노드로 표현하면 이렇게 된다.
```
{
    type: 'div',
    props: { className: 'container' },
    children: [
        {
            type: 'h1',
            props: {},
            children: ['제목']
        },
        {
            type: 'p',
            props: {},
            children: ['내용']
        }
    ]
}
```
- type: HTML 태그 이름 ('div', 'p', 'span' 등)
- props: 요소의 속성들 (class, id, style 등)
- children: 자식 요소들의 배열

### normalizeVNode
- Virtual DOM 노드를 일관된 형태로 표준화
- 다양한 입력 타입(null, 문자열, 숫자, 함수 등)을 처리
- 재귀적으로 작동해서 모든 자식 노드도 표준화
- 렌더링 전에 데이터 구조를 일관되게 만드는 게 목적

```js
export function normalizeVNode(vNode) {
  // 기본 타입 처리
  const isNullOrBoolean = (value) =>
    value == null || typeof value === "boolean";
  const isPrimitive = (value) =>
    typeof value === "string" || typeof value === "number";
  const isFunction = (value) => typeof value === "function";
  const isComponent = (value) =>
    value && typeof value === "object" && isFunction(value.type);

  // 빈 값 처리
  if (isNullOrBoolean(vNode)) {
    return "";
  }

  // 원시 타입 처리
  if (isPrimitive(vNode)) {
    return String(vNode);
  }

  // 함수형 컴포넌트 직접 호출 케이스
  if (isFunction(vNode)) {
    return normalizeVNode(vNode());
  }

  if (vNode && typeof vNode === "object") {
    // 함수형 컴포넌트 객체 케이스
    if (isComponent(vNode)) {
      const props = {
        ...vNode.props,
        children: vNode.children,
      };
      return normalizeVNode(vNode.type(props));
    }

    // children 정규화
    const normalizedChildren = normalizeChildren(vNode.children);

    return {
      type: vNode.type,
      props: vNode.props,
      children: normalizedChildren,
    };
  }

  return vNode;
}

// children 정규화
function normalizeChildren(children) {
  const childArray = Array.isArray(children) ? children : [];
  return childArray
    .map((child) => normalizeVNode(child))
    .filter((child) => {
      const isEmptyValue =
        child === null ||
        child === undefined ||
        child === false ||
        child === "";
      return !isEmptyValue;
    });
}

```
- 함수 컴포넌트를 createVNode, normalizeVNode에 통과시킨 예시
```js
// 함수 컴포넌트
const Welcome = () => createVNode('h1', null, '안녕하세요!');

// 이 함수를 createVNode로 생성한 Virtual DOM
const vNode = createVNode('div', null,
    Welcome,  // 아직 실행되지 않은 함수
    '다른 내용'
);

// createVNode 결과: 
{
    type: 'div',
    props: {},
    children: [
        () => ({ type: 'h1', props: {}, children: ['안녕하세요!'] }),  // 아직 함수
        '다른 내용'
    ]
}

// normalizeVNode를 통과하면 함수가 실행됨
// normalizeVNode 결과:
{
    type: 'div',
    props: {},
    children: [
        { type: 'h1', props: {}, children: ['안녕하세요!'] },  // 함수가 실행되어 실제 노드로
        '다른 내용'
    ]
}
```

### createElement

- Virtual DOM 노드를 실제 DOM 요소로 변환
- 노드 타입에 따라 다르게 처리(텍스트, 요소, 프래그먼트 등)
- props를 실제 DOM 속성으로 변환
- 자식 요소들을 재귀적으로 생성

```js
// 속성 업데이트 함수 (이벤트 처리 제외)
function updateAttributes($el, props) {
  if (!props) return;

  Object.entries(props).forEach(([key, value]) => {
    if (key === "className") {
      $el.className = value;
    } else {
      $el.setAttribute(key, value);
    }
  });
}

// vNode를 실제 DOM으로 변환하는 함수
export function createElement(vNode) {
  // null, boolean 처리
  if (vNode == null || typeof vNode === "boolean") {
    return document.createTextNode("");
  }

  // 문자열이나 숫자는 텍스트 노드로
  if (typeof vNode === "string" || typeof vNode === "number") {
    return document.createTextNode(String(vNode));
  }

  // 배열은 DocumentFragment로
  if (Array.isArray(vNode)) {
    const fragment = document.createDocumentFragment();
    vNode.forEach((child) => fragment.appendChild(createElement(child)));
    return fragment;
  }

  // 실제 DOM 요소 생성
  const $el = document.createElement(vNode.type);
  updateAttributes($el, vNode.props);

  // 자식 요소들 처리
  if (vNode.children) {
    vNode.children.forEach((child) => {
      $el.appendChild(createElement(child));
    });
  }

  return $el;
}
```

```html
// createElement 실행 후 생성되는 실제 DOM:
<div class="container">
    <header class="header">
        <h1>제목</h1>
    </header>
    <main>
        <p>내용 1</p>
        항목 1
        항목 2
        <p>내용 2</p>
    </main>
</div>
```

### updateElement
- 이전 Virtual DOM 트리와 새로운 Virtual DOM 트리를 비교
- 실제로 변경된 부분만 찾아내서 최소한의 실제 DOM 업데이트 수행
```js
import { addEvent, removeEvent } from "./eventManager";
import { createElement } from "./createElement.js";

function updateAttributes(target, originNewProps, originOldProps) {
  const newProps = originNewProps || {};
  const oldProps = originOldProps || {};

  // 이전 속성 제거
  Object.keys(oldProps).forEach((key) => {
    if (key.startsWith("on")) {
      const eventType = key.slice(2).toLowerCase();
      if (oldProps[key]) {
        removeEvent(target, eventType, oldProps[key]);
      }
    } else if (!(key in newProps)) {
      if (key === "className") {
        target.className = "";
      } else {
        target.removeAttribute(key);
      }
    }
  });

  // 새로운 속성 추가/업데이트
  Object.entries(newProps).forEach(([key, value]) => {
    if (oldProps[key] === value) return;

    if (key.startsWith("on") && typeof value === "function") {
      const eventType = key.slice(2).toLowerCase();
      if (oldProps[key]) {
        removeEvent(target, eventType, oldProps[key]);
      }
      addEvent(target, eventType, value);
    } else if (key === "className") {
      target.className = value;
    } else {
      if (value === false || value === null || value === undefined) {
        target.removeAttribute(key);
      } else {
        target.setAttribute(key, value);
      }
    }
  });
}

export function updateElement(parentElement, newNode, oldNode, index = 0) {
  // 노드가 모두 없는 경우
  if (!oldNode && !newNode) {
    return;
  }

  // 이전 노드만 있는 경우 (삭제)
  if (oldNode && !newNode) {
    parentElement.removeChild(parentElement.childNodes[index]);
    return;
  }

  // 새로운 노드만 있는 경우 (추가)
  if (!oldNode && newNode) {
    parentElement.appendChild(createElement(newNode));
    return;
  }

  // 둘 다 텍스트 노드인 경우
  if (typeof newNode === "string" || typeof newNode === "number") {
    if (oldNode !== newNode) {
      const newTextNode = document.createTextNode(String(newNode));
      parentElement.replaceChild(newTextNode, parentElement.childNodes[index]);
    }
    return;
  }

  // 노드 타입이 다른 경우
  if (newNode.type !== oldNode.type) {
    parentElement.replaceChild(
      createElement(newNode),
      parentElement.childNodes[index],
    );
    return;
  }
  // 속성 업데이트
  const element = parentElement.childNodes[index];
  updateAttributes(element, newNode.props, oldNode.props);

  // 자식 노드 비교 및 업데이트
  const newChildren = newNode.children || [];
  const oldChildren = oldNode.children || [];
  const maxLength = Math.max(newChildren.length, oldChildren.length);

  Array.from({ length: maxLength }).forEach((_, i) => {
    updateElement(element, newChildren[i] || null, oldChildren[i] || null, i);
  });
}

```


### renderElement
- 전체 렌더링 프로세스를 조정
- Virtual DOM을 실제 DOM으로 변환하는 과정을 관리
- 이벤트 리스너 설정 포함 (현 포스팅에서는 생략)

```js
import { createElement } from "./createElement";
import { normalizeVNode } from "./normalizeVNode";
import { updateElement } from "./updateElement";

const vNodeMap = new WeakMap();

export function renderElement(vNode, container) {
  // vNode를 정규화
  const normalizedNode = normalizeVNode(vNode);
  const currentVNode = vNodeMap.get(container);

  if (!currentVNode) {
    // 최초 렌더링
    container.innerHTML = "";
    const element = createElement(normalizedNode);
    container.appendChild(element);
  } else {
    // 업데이트: 변경된 부분만 업데이트
    updateElement(container, normalizedNode, currentVNode);
  }

  // 현재 vNode 저장
  vNodeMap.set(container, normalizedNode);
}
```

-	WeakMap을 사용해 container별로 현재 vNode를 저장
메모리 관리를 위해 WeakMap 사용 (container가 사라지면 자동으로 정리 - container 요소가 더 이상 필요 없어서 제거되면 WeakMap에 저장된 관련 Virtual DOM 데이터도 자동으로 가비지 컬렉션의 대상이 됨)

- 렌더링 프로세스
1. 먼저 vNode를 정규화
2. 최초 렌더링인지 업데이트인지 구분
3. 최초: createElement로 새로 생성
4. 업데이트: updateElement로 변경된 부분만 업데이트

### (참고) Map과 WeakMap의 차이
1. 메모리 관리 방식
```js
// Map의 경우
const regularMap = new Map();
let obj = { data: '데이터' };
regularMap.set(obj, '값');
obj = null;  // obj 참조를 제거해도
// regularMap에는 여전히 데이터가 남아있음

// WeakMap의 경우
const weakMap = new WeakMap();
let obj = { data: '데이터' };
weakMap.set(obj, '값');
obj = null;  // obj 참조를 제거하면
// weakMap에서도 자동으로 데이터가 정리됨
```

2. 키(Key)로 사용할 수 있는 타입
```js
// Map은 모든 타입을 키로 사용 가능
const map = new Map();
map.set('문자열', 값1);
map.set(42, 값2);
map.set(true, 값3);

// WeakMap은 객체만 키로 사용 가능
const weakMap = new WeakMap();
weakMap.set({ key: 'obj' }, 값1);  // 가능
weakMap.set('문자열', 값2);        // 에러 발생!
```

3. 순회 가능 여부
```js
// Map은 순회 가능
const map = new Map();
map.set('키1', '값1');
map.set('키2', '값2');

for (let [key, value] of map) {
    console.log(key, value);
}

// WeakMap은 순회 불가능
const weakMap = new WeakMap();
// for...of 사용 불가
// keys(), values(), entries() 메서드 없음
```

결론:

Map: 모든 종류의 키를 사용, 데이터 유지, 순회 가능
WeakMap: 객체만 키로 사용, 자동 메모리 정리, 순회 불가능

용도:

Map: 데이터를 안정적으로 보관하고 관리할 때
WeakMap: 임시 데이터를 저장하거나 메모리 관리가 중요할 때


## 동작 과정 정리

먼저 `createVNode` 함수로 Virtual DOM 노드를 생성한다. 이 함수는 HTML 구조를 JavaScript 객체 형태로 표현하는데, type(태그 이름), props(속성들), children(자식 요소들)을 받아서 **하나의 객체**로 만들어낸다.

생성된 Virtual DOM 노드는 `normalizeVNode` 함수를 통과한다. 이 함수는 다양한 형태의 입력값(null, 문자열, 숫자, 함수, 배열 등)을 **일관된 형태로 정규화**한다. 예를 들어 조건부 렌더링으로 인한 null값을 제거하고, 동적으로 생성되는 함수 컴포넌트를 실행하여 실제 노드로 변환한다.

정규화된 Virtual DOM은 `createElement` 함수를 통해 **실제 DOM 요소로 변환**된다. 이 함수는 Virtual DOM의 type에 따라 실제 DOM 요소를 생성하고, props를 적용하며, children을 재귀적으로 처리한다.

변경사항이 생기면 `updateElement` 함수가 이전 Virtual DOM과 새로운 Virtual DOM을 비교하여 **실제로 변경된 부분만 찾아낸다**. 이때 노드의 추가, 삭제, 수정을 각각의 경우에 맞게 처리하며, 특히 불필요한 DOM 조작을 최소화하기 위해 같은 타입의 노드는 속성만 업데이트하고, 자식 노드들은 재귀적으로 비교한다.

마지막으로 이 모든 과정을 `renderElement` 함수가 관리한다. 이 함수는 WeakMap을 사용해 각 컨테이너의 현재 Virtual DOM 상태를 저장하고, 최초 렌더링인지 업데이트인지를 판단하여 적절한 함수를 호출한다. WeakMap을 사용함으로써 컨테이너가 제거될 때 관련된 Virtual DOM 데이터도 자동으로 정리되어 메모리 관리가 효율적으로 이루어진다.

# 후기 (깨달은 점)
**1.리액트는 정말 성능이 좋은가? 아니다. ** (과제를 하면서 깨달은 점) 

하지만 우리는 왜 리액트를 쓸까?

그 이유는 유지보수와 개발 효율성 때문이다. 바닐라 자바스크립트로 DOM을 직접 조작하면 코드가 복잡해지고, 상태 관리가 어려워진다.** 리액트는 선언적인 방식으로 UI를 구성할 수 있게 해주고, 컴포넌트 단위로 코드를 관리할 수 있게 해준다.** -> 겁나 편하고 직관적

**2. Virtual DOM은 객체 덩어리 **

이번 과제를 통해 Virtual DOM이 생각보다 _무서운 게 아니라는 걸_ 깨달았다. 결국 JavaScript 객체의 덩어리이고, 이 객체를 효율적으로 비교하고 실제 DOM에 반영하는 것이 핵심이다. 브라우저가 렌더링하는 실제 DOM과 메모리상의 가상 DOM을 비교해서, 정말 필요한 부분만 업데이트하는 것이다.

**3. 성능 최적화의 필요성**

성능 최적화는 결국 불필요한 렌더링을 줄이는 게 핵심이다. Virtual DOM도 결국 JavaScript 객체를 비교하는 작업이기 때문에, 비교 작업 자체가 비용이다. 따라서:

- 컴포넌트를 적절히 분리하여 필요한 부분만 리렌더링되게 하기
- useMemo, useCallback 등의 메모이제이션 활용하기
- 상태 관리를 효율적으로 하여 불필요한 리렌더링 방지하기

이런 최적화 기법들이 중요하다는 것을 이해하게 되었다. (다음 과제하고도 이어지는 내용..ㄷㄷ)
이번 과제를 통해 프레임워크의 내부 동작 원리를 이해하게 되었고, 이는 앞으로 리액트를 더 효율적으로 사용하는 데 큰 도움이 될 것 같다.

<br />

솔직히 말하자면 이번 과제는 혼자 힘으로 구현하지 못했다. 

발제 자료부터 시작해서 수많은 구글링, 그리고 Claude의 도움까지 받아가며 간신히 테스트 코드를 통과할 수 있었다. 하지만 이런 과정을 통해 그동안 피상적으로만 알고 있던 Virtual DOM의 개념을 깊이 있게 이해할 수 있었고, 실제 구현 로직까지 파고들 수 있어서 매우 의미 있는 시간이었다.

다음 주 발제와 과제는 리액트의 성능 최적화가 주제다. 

이번 Virtual DOM 과제에서 배운 내용이 성능 최적화를 이해하는 데 좋은 기반이 될 것 같다. 시간이 넉넉히 주어진 만큼, 이번에도 최선을 다해 도전해봐야겠다!

![](https://velog.velcdn.com/images/khy2106/post/6c325476-6cf9-4536-99f6-9cfa0029eec5/image.png)
