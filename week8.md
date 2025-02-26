# 들어가면서

이번 주차에서는 지난 7주차에 이어 **프론트엔드 테스트 코드**에 관한 내용을 학습했다.

**"테스트는 언제 도입해야 할까? 테스트가 중요하다고 하면서도 실무에서 TDD를 도입하거나 테스트 코드를 작성하는 팀이 왜 그리 드문 걸까?"** 이는 테스트 코드를 공부하기 전 항상 품었던 의문이었다.

이번 과제를 통해 단순히 테스트 코드 작성법을 넘어, 테스트 전략 수립과 TDD의 실질적 활용법까지 배울 수 있었다. 특히 모든 상황에 TDD를 적용하는 것이 아니라, 특정 유형의 과제와 환경에 적합한 테스트 방식을 선택해야 한다는 점이 인상적이었다. 또한 테스트 역시 개발 리소스의 일부로, 비용과 속도를 고려한 효율적인 **설계**가 필요하다는 사실을 깨달았다.

이번 포스팅에서는 금주 학습한 내용 중에서도 나를 포함한 많은 프론트엔드 개발자들이 궁금해하는 **TDD의 개념과 활용 예시**에 대해 간단히 다루어 볼 예정이다.

<hr />

# 8주차 WIL
## TDD, 넌 도대체 무엇이냐
위에 언급했듯 TDD라는 단어, 많이 들어봤는데 정작 무엇의 약자인지는 잘 모르고 있었다. TDD는 **Test-Driven Development**의 약자로, 말 그대로 **테스트를 통해 개발을 이끌어가는 방법론**이다. 

전통적인 개발 방식과 달리, 코드를 작성하기 전에 테스트 코드를 먼저 작성하여 개발의 방향성을 설정한다.

> TDD(Test-Driven Development): 테스트를 기반으로 진행되는 개발 방법론. 실제 코드를 작성하기 전에 테스트 코드를 먼저 작성하고, 그 테스트를 통과하는 것을 목적으로 기능을 구현하는 방법



## TDD의 원리와 사이클
프론트엔드 개발자로서 특정 기능을 구현해야 하고, 그에 따른 컴포넌트를 만들어내야 한다고 가정해 보자. 일반적으로는 기획 회의를 통해 만들어진 기능 명세서, 그리고 UI 디자인을 보고 바로 개발을 시작하게 된다.

하지만 TDD가 도입되면 개발 프로세스가 더 체계적이고 정밀해진다. TDD는 다음과 같은 **"레드-그린-리팩터(Red-Green-Refactor)" **사이클을 따른다:

- **Red (실패)**: 먼저 실패하는 테스트 코드를 작성한다. 이 단계에서는 아직 기능이 구현되지 않았으므로 테스트가 실패하는 것이 당연하다.

- **Green (성공)**: 테스트를 통과할 수 있는 코드를 작성한다. 

- **Refactor (개선)**: 테스트를 통과한 코드를 리팩토링하여 코드 품질을 개선한다. 중복을 제거하고, 가독성을 높이며, 효율성을 개선한다. 이 과정에서도 모든 테스트가 계속 통과해야 한다.

![](https://velog.velcdn.com/images/khy2106/post/8bc2308f-35f5-41ef-bca4-73d2d396986b/image.png)
사진 출처 : [TechTarget](https://www.techtarget.com/searchsoftwarequality/definition/test-driven-development) 

## TDD의 장점

1. **설계 품질 향상**: 테스트를 먼저 작성하면 자연스럽게 모듈화된, 테스트하기 쉬운 코드를 작성하게 된다. 이는 결과적으로 더 좋은 설계로 이어진다.

2. **버그 감소**: 각 기능마다 테스트를 작성하므로, 버그를 조기에 발견하고 수정할 수 있다.

3. **리팩토링 안전성**: 잘 작성된 테스트는 코드를 리팩토링할 때 안전망 역할을 한다. 변경 후에도 모든 테스트가 통과하면, 기능이 올바르게 작동한다고 확신할 수 있다.

4. **문서화 효과**: 테스트 코드는 코드의 사용 방법과 의도를 보여주는 문서 역할을 한다.

## 실무에서의 적용

실제 프로젝트에서 TDD를 100% 적용하는 것은 쉽지 않을 수 있다. 특히 시간 제약이 있거나 요구사항이 완벽하게 정의되지 않은 상태에서 개발이 필요한 경우에는 TDD 방식이 비효율적일 수 있다. 따라서 다음과 같은 상황에서 **선택적으로 TDD를 적용**하는 것이 현실적인 접근법이라고 한다.

1. **핵심 비즈니스 로직**: 복잡한 계산, 데이터 변환 등 중요한 비즈니스 로직에 TDD 적용

2. **버그 수정**: 버그를 재현하는 테스트를 먼저 작성한 후 수정

3. **재사용 가능한 컴포넌트/유틸리티**: 여러 곳에서 사용되는 공통 코드


### 실제 적용 예시

**1. 🔴 Red 단계 : 테스트 작성**

```js
// discountCalculator.test.js
import { calculateDiscount } from './discountCalculator';

describe('장바구니 할인 계산기', () => {
  test('5만원 미만은 할인 없음', () => {
    expect(calculateDiscount(30000)).toBe(0);
    expect(calculateDiscount(49999)).toBe(0);
  });
  
  test('5만원 이상 10만원 미만은 5% 할인', () => {
    expect(calculateDiscount(50000)).toBe(2500);
    expect(calculateDiscount(80000)).toBe(4000);
  });
  
  test('10만원 이상 20만원 미만은 10% 할인', () => {
    expect(calculateDiscount(100000)).toBe(10000);
    expect(calculateDiscount(150000)).toBe(15000);
  });
  
  test('20만원 이상은 15% 할인', () => {
    expect(calculateDiscount(200000)).toBe(30000);
    expect(calculateDiscount(500000)).toBe(75000);
  });
  
  test('회원 등급에 따른 추가 할인 적용', () => {
    // 일반 회원 - 추가 할인 없음
    expect(calculateDiscount(100000, 'REGULAR')).toBe(10000);
    // VIP 회원 - 5% 추가 할인
    expect(calculateDiscount(100000, 'VIP')).toBe(15000);
    // VVIP 회원 - 10% 추가 할인
    expect(calculateDiscount(100000, 'VVIP')).toBe(20000);
  });
});
```

**2. 🟢 Green 단계 : 테스트 통과 코드 작성**

```js
// discountCalculator.js
export function calculateDiscount(amount, membershipLevel = 'REGULAR') {
  // 기본 할인율 계산
  let discountRate = 0;
  
  if (amount >= 200000) {
    discountRate = 0.15;
  } else if (amount >= 100000) {
    discountRate = 0.10;
  } else if (amount >= 50000) {
    discountRate = 0.05;
  }
  
  // 회원 등급에 따른 추가 할인
  let additionalDiscount = 0;
  if (membershipLevel === 'VIP') {
    additionalDiscount = 0.05;
  } else if (membershipLevel === 'VVIP') {
    additionalDiscount = 0.10;
  }
  
  // 최종 할인 금액 계산
  const totalDiscountRate = discountRate + additionalDiscount;
  return Math.floor(amount * totalDiscountRate);
}
```
(리팩토링 파트를 위해 극단적인 예시를 가져왔다 ㅠ)

**3. ⚒️ Refactor 단계: 코드 개선**

```js
// discountCalculator.js (리팩토링 후)
// 금액별 할인율 정의
const DISCOUNT_TIERS = [
  { threshold: 200000, rate: 0.15 },
  { threshold: 100000, rate: 0.10 },
  { threshold: 50000, rate: 0.05 },
  { threshold: 0, rate: 0 }
];

// 회원 등급별 추가 할인율 정의
const MEMBERSHIP_DISCOUNTS = {
  'REGULAR': 0,
  'VIP': 0.05,
  'VVIP': 0.10
};

export function calculateDiscount(amount, membershipLevel = 'REGULAR') {
  // 금액에 해당하는 할인율 찾기
  const { rate: baseDiscountRate } = DISCOUNT_TIERS.find(
    tier => amount >= tier.threshold
  );
  
  // 회원 등급에 따른 추가 할인율
  const additionalRate = MEMBERSHIP_DISCOUNTS[membershipLevel] || 0;
  
  // 최종 할인 금액 계산
  return Math.floor(amount * (baseDiscountRate + additionalRate));
}
```


실제 개발 환경에서는 TDD를 위 예시처럼 Red-Green-Refactor 사이클로 진행한다.

<hr />

# 마무리하면서

이번 주차에서는 막연하기만 했던 TDD의 개념과 적용 방법을 배울 수 있어서 좋았다. 솔직히 말하면 간단한 비즈니스 로직을 예시로 들면서도**_ '이런 식으로 개발을 하는 게 정말 가능하다고?'_**라는 생각이 들었던 것 같다. 

특히 우리 회사의 경우 즉석에서 튀어나오는 아이디어를 바탕으로 빠르게 기능 개발이 되어야 하고, 정확한 기능 명세 없이 작업을 하는 경우도 자주 있어서 더욱 그랬던 게 아닌가 싶다.

하지만 앞서 언급했듯 정확한 요구사항이 있고, 정확도를 지켜야 하는 작업의 경우 이러한 개발 방식이 꼭 필요할 거라는 생각이 든다. 

금융 업무와 같이 한 치의 오차도 허용되지 않는 비즈니스의 경우, 이런 방식을 도입해서 정확도를 높이고 있지 않나 예상해 본다. 특히 돈과 직결되는 계산 로직이나 중요한 데이터를 처리하는 부분에서는 테스트가 충분히 뒷받침되어야 안정적인 서비스를 제공할 수 있을 것이다.

언젠가는 TDD를 도입한 개발 조직에서 업무를 해 보고 싶다는 생각이 든다. 처음에는 생산성이 떨어지는 것처럼 보일 수도 있겠지만, 시간이 지날수록 안정적인 코드베이스와 지속 가능한 개발 문화가 만들어지지 않을까?  프론트엔드 TDD에 대해 알게 되었고, 개발 프로세스와 코드 품질에 대해 더 깊이 생각할 수 있어서 유용한 한 주였다.

<hr />
항해 플러스 5기 모집 중입니다 :)

https://hanghae99.spartacodingclub.kr/plus/fe

(추천인 코드 ELPVP6 를 입력하시면 수강료를 20만원 할인받을 수 있습니다)
