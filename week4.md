# 들어가면서

바닐라 자바스크립트로 SPA와 React Hook을 직접 구현하면서 작동 원리를 익혔던 Chapter 1을 마무리했다. 이제 Chapter 2에서는 함수형 프로그래밍 원리를 기반으로 **클린 코드와 리팩토링**을 학습할 예정이다.

4주차 발제에서 테오 코치님은 클린 코드의 정의부터 필요성, 실제 적용 방법까지 체계적으로 설명해 주셨다. 특히 깔끔하고 일목요연한 PPT 자료는 내 취향과 완벽하게 일치했다. 다음에 발표 자료를 만들 때 좋은 참고가 될 것 같다.

개발자라면 누구나 `클린 코드`라는 말을 귀에 딱지가 앉도록 들어봤을 것이다. 그만큼 중요한 개념이고, 관련 도서나 영상, 블로그 글도 셀 수 없이 많다. 하지만 아이러니하게도 자료를 더 찾아볼수록 실제로 무엇을, 어떻게 적용해야 할지 더욱 혼란스러워지곤 했다. 그런 내게 테오님의 발제와 자료들은 좋은 방향성을 제시해 주었다. 군더더기가 없어서 내용이 한결 명확하게 와닿았다.

# 4주차 과제 WIL

## 과제 소개 
4주차에는 처음으로 평일 내내 야근을 했다. 주 2-3회 야근은 해봤지만 연속 야근은 처음이었다. 하필 관심 있는 주제를 다룰 때 회사 일이 겹쳐 속상했지만, 늦은 퇴근 후에도 공부와 과제를 이어간 내 모습이 대견하다.

![](https://velog.velcdn.com/images/khy2106/post/bdaedc73-dc4c-416a-94d4-fc95c07e381e/image.png)

예전부터 나는 공부할 때 손으로 직접 필기하며 내용을 익히는 것을 선호했다. 직장 생활을 시작하면서 필기할 기회가 줄어들긴 했지만, 이번만큼은 개념을 확실히 이해하고 싶어서 발제 자료를 보며 노트 필기를 했다.

4주차 과제는 **누가 봐도 클린하지 않다고 말할 수 있는 코드를, 클린 코드 원칙에 따라 리팩토링하는 것** 이었다. 처음에는 Chapter 1에 비해 쉬울 것이라 예상했다. (보안상 전체 코드는 공개하지 않겠지만) 대략 이런 모습이었다.

![](https://velog.velcdn.com/images/khy2106/post/c8ceaf8c-50eb-4981-84e0-6b21e998ed66/image.png)

이런 형태의 코드가 200줄이나 이어졌다. 함수들은 어떤 기준도 없이 뒤죽박죽 나열되어 있었고, 코드를 제대로 분석하기 전에는 각 함수의 목적과 기능을 전혀 파악할 수 없었다. 


## 클린코드 적용해 보기
발제 자료를 토대로 리팩토링을 시작했다. 다음은 클린 코드 작성의 주요 원칙과 적용 방법이다.

<hr />

**1. 의미 있는 이름 사용**
- 목적과 역할이 명확히 드러나는 구체적인 이름을 사용한다
- 불필요한 정보는 제외하여 간결성을 유지한다

```js
// Before
var sel, addBtn, cartDisp, sum;


// After
const elements = {
  productSelect: createUIElement("select",...),
  addButton: createUIElement("button",...),
  cartDisplay: createUIElement("div",...),
  total: createUIElement("div",...)
}
```
<hr />

**2. 함수의 단일 책임**
- 각 함수는 하나의 작업만 수행한다
- 함수 길이는 20-30줄을 초과하지 않는다
- 매개변수는 3개 이하로 제한한다
- 부수 효과를 최소화한다
- 일관된 추상화 수준을 유지한다

```js
// Before
function calcCart() {
  // 100줄 이상의 코드
  // 할인 계산, UI 업데이트, 포인트 계산 등 여러 작업 수행
}

// After
function getIndividualDiscountRate(product, quantity) {...}
function applyBulkDiscount({...}) {...}  
function applyTuesdayDiscount({...}) {...}
function renderTotal(total, totalEl, discountRate) {...}
```

<hr />

**3. 중복 제거 **
- 반복되는 코드 패턴은 공통 함수나 모듈로 분리한다


```js
// Before

// 상품 추가 시 수량 파싱
var newQty = parseInt(item.querySelector("span").textContent.split("x ")[1]) + 1;

// 수량 변경 시 수량 파싱
var newQty = parseInt(itemElem.querySelector("span").textContent.split("x ")[1]) + qtyChange;

// 삭제 시 수량 파싱
var remQty = parseInt(itemElem.querySelector("span").textContent.split("x ")[1]);


// After

// 수량 관련 헬퍼 함수로 중복 제거
function getItemQuantity(itemElement) {
  return parseInt(itemElement.querySelector("span").textContent.split("x ")[1]);
}

// 수량 업데이트 관련 헬퍼 함수
function updateItemQuantity(itemElement, product, newQty) {
  itemElement.querySelector("span").textContent =
    `${product.name} - ${product.val}원 x ${newQty}`;
}

// 사용 예시
const currentQty = getItemQuantity(itemElement);
const newQty = currentQty + change;
updateItemQuantity(itemElement, product, newQty);
```

<hr />

**4. 자체 설명적인 코드**

- 코드 자체로 의도와 동작이 명확히 드러나도록 작성한다
- 주석이 필요한 경우 구현 방법(how)보다 이유(why)를 설명한다


```js
// Before
if (q >= 10) {
  if (curItem.id === "p1") disc = 0.1;
  else if (curItem.id === "p2") disc = 0.15;
  // ...
}

// After
function getIndividualDiscountRate(product, quantity) {
  return quantity >= 10 ? (product.discountRate ?? 0) : 0;
}
```

<hr />

**5. 조건문 단순화 **
- 중첩을 최소화하고 early return을 활용한다

```js
// Before
if (itemCnt >= 30) {
  var bulkDisc = totalAmt * 0.25;
  var itemDisc = subTot - totalAmt;
  if (bulkDisc > itemDisc) {
    totalAmt = subTot * (1 - 0.25);
    discRate = 0.25;
  } else {
    discRate = (subTot - totalAmt) / subTot;
  }
} else {
  discRate = (subTot - totalAmt) / subTot;
}

// After
function applyBulkDiscount({ subTotal, totalAmt, itemCount, items }) {
  if (itemCount < 30) {
    const existedRate = subTotal === 0 ? 0 : (subTotal - totalAmt) / subTotal;
    return { total: totalAmt, discountRate: existedRate, items };
  }

  const bulkDiscountAmount = subTotal * 0.25;
  const individualDiscountAmount = subTotal - totalAmt;

  const shouldApplyBulkDiscount = bulkDiscountAmount > individualDiscountAmount;
  if (shouldApplyBulkDiscount) {
    return { total: subTotal * 0.75, discountRate: 0.25, items };
  }

  const existedRate = (subTotal - totalAmt) / subTotal;
  return { total: totalAmt, discountRate: existedRate, items };
}
```
(근데 이건 early return만 사용했지 리얼 클린 코드인지에 대해서는 자신이 없다..ㅠ)


<hr />

**6. 적절한 추상화**
- 비즈니스 로직과 구현 세부사항을 분리한다
- 과도한 추상화는 지양한다
- 동일 수준의 추상화는 한 곳에서 처리한다

```js
// Before

// UI 로직과 비즈니스 로직이 섞여 있음
function main() {
  // DOM 조작
  // 상태 관리
  // 이벤트 핸들링
  // 할인 계산
  // 전부 한 함수에서 처리
}


// After

// UI 관련
function createUIElements() {...}
function appendUIElements(root, elements) {...}

// 비즈니스 로직
function calculateCart(cartItems, products) {...}

// 상태 관리
function createStore(initialState) {...}

// 이벤트 핸들링
function setupAddButtonEvent(elements, store) {...}
function setupCartButtonEvents(elements, store) {...}
```

<hr />

### 아쉬웠던 점, 더 공부해야 하는 부분

이 파트는 4주차에 진행됐던 준일코치님의 멘토링과 이어서 얘기해 보고자 한다.

**1. 모호했던 `추상화`의 개념**

발제 자료에서 '추상화'라는 용어가 자주 등장했지만, 인터넷 검색으로는 그 의미를 정확히 파악하기가 어려웠다.

![](https://velog.velcdn.com/images/khy2106/post/c0ebb0ff-f820-42a9-be24-3670d1ab63f0/image.png)
다행히 준일 코치님께서 멘토링 노트에 명확한 예시와 함께 설명해 주셔서 큰 도움이 되었다. 추상화란 복잡한 내용을 숨기고 핵심적인 내용만 명확하게 드러내는 과정이다. 즉, 한눈에 이해할 수 있도록 만드는 것이 추상화의 핵심이라고 할 수 있다.

과제를 진행하면서 '**추상화를 어떻게 적용해야 하지?**'라는 의문이 계속 들었던 것 같다. 앞으로 실제 코드에 조금씩 적용해보면서 익숙해져야 할 부분이라고 생각한다.

**2. 코드 구조화 **

테오 코치님의 발제 자료에서는 사람이 정보를 시각적으로 파악하는 과정에 대해 다루었다. 같은 위치에 묶여 있으면 하나의 그룹으로 인식하듯이, 코드의 시각적 배치도 중요하다는 것이다.

하지만 실제로 리팩토링을 하려고 하니, 단순히 함수를 묶는 것을 넘어서 어떤 기준으로 함수들을 구조화해야 할지 고민이 되었다. 5주차에서 배울 디자인 패턴과 함수형 프로그래밍을 통해 이런 고민들을 해결해 나가볼 생각이다.

![](https://velog.velcdn.com/images/khy2106/post/b39a2e47-e26f-4812-b57e-72a91d2be270/image.png)

**3. early return 패턴 **

**early return은 if문의 중첩을 줄이고 코드의 가독성을 높이는 패턴**이다. 조건문을 중첩해서 쓰는 대신, 조건에 맞지 않는 경우를 먼저 return하여 로직을 빨리 종료하는 방식이다.

돌이켜 보니 실무에서 나도 모르게 중첩 if문을 사용하고 있었다. 이번 과제의 첫 리팩토링 코드도 마찬가지였다. 하지만 준일 코치님의 멘토링 조언을 듣고 early return을 적용해 중첩 if문을 최대한 줄여보았다.

확실히 코드가 훨씬 깔끔해지고 이해하기 쉬워진 것 같다. 그동안 early return이라는 개념을 모르고 습관적으로 중첩 if문을 써온 게 아쉽긴 하지만, 이제라도 알게 되어 다행이다. 

**4. 부수 효과 제거 **

**부수 효과란, 함수가 자신의 범위 밖에서 어떤 값을 변경하거나 동작에 영향을 미치는 것**을 말한다. 이는 예측하기 어려운 동작을 만들어내고, 함수의 재사용성과 테스트 용이성을 떨어뜨린다.

```js
// B부수 효과가 있는 함수
let totalPrice = 0;

function addToTotal(price) {
  totalPrice += price; // 전역 변수를 수정하는 부수 효과
}

// 부수 효과 제거
function calculateNewTotal(currentTotal, price) {
  return currentTotal + price; // 순수 함수
}
```
함수는 항상 입력값에 따른 결과값만 반환하고, 외부 상태를 변경하지 않도록 작성하는 것이 좋다. 이런 함수를 순수 함수라고 하는데, 이는 5주차에서 배울 함수형 프로그래밍의 핵심 개념이기도 하다.

실무 경험으로 무의식중에 let 사용을 피하고 있었는데, 그 이유를 확실히 알고 나니 속이 시원하다. 앞으로도 부수 효과가 없는 순수 함수를 사용해야겠다는 결심이 선다.

![](https://velog.velcdn.com/images/khy2106/post/6537cefb-716d-4b90-bf68-2e45c950da4d/image.png)


# 마무리하면서
4주차 과제를 통해 '클린 코드'가 단순히 예쁘게 보이는 코드가 아니라(ㅋㅋ), 실제로 유지보수와 가독성 측면에서 큰 차이를 만드는 중요한 개념이라는 것을 체감할 수 있었다. 특히 함수의 단일 책임, early return 패턴, 부수 효과 제거하기 같은 것들은 당장 실무에서도 적용해볼 수 있을 것 같다.

리팩토링을 하면서 가장 크게 느낀 점은, 좋은 코드를 작성하는 건 결국 꾸준한 학습과 연습이 필요하다는 것이다. 이번 과제에서 배운 개념들을 앞으로도 계속 실천하면서, 더 나은 개발자가 되도록 노력해야겠다.

특별히 야근이 많았던 한 주였지만, 그 와중에도 과제를 완수하고 새로운 것을 배울 수 있어서 뿌듯했다. 다음 주차에서는 또 어떤 새로운 도전이 기다리고 있을지 기대된다.


_P.S : 다 적고 보니 내 글도 가독성이 좋고 그룹핑이 잘 된 글인지 다시 한 번 되돌아보게 된다...ㅎ
_

