# 들어가면서
`디자인 패턴`하면 정처기 필기 때 죽어라 외웠던 GoF(Gang of Four)가 떠오른다. 싱글톤, 팩토리, 어댑터... 당시에는 시험 합격을 위해 각각의 특징도 제대로 이해하지 못한 채 무작정 암기했던 기억이 난다.

**그런데 정작 실무에서는 이러한 패턴들을 하나도 적용해본 기억이 없다.** 프론트엔드 개발자라면 공감할 것이다. _수많은 디자인 패턴이 존재하는데, 왜 우리는 실제 업무에서 이런 패턴들을 적용해본 경험이 없는 걸까?_ 이번 주 테오 코치님의 발제 자료를 바탕으로 과제를 진행하면서, 이 질문에 대한 답을 찾고 실무 적용 방법까지 알게 되었다. 답을 미리 말하자면, **우리는 모르는 사이에 이미 디자인 패턴을 사용하고 있었다.**

함수형 프로그래밍도 마찬가지다. 나는 줄곧 함수형 프로그래밍이 map, reduce 같은 고차 함수를 사용하거나 for문 같은 명령형 프로그래밍 기법을 피하는 것이라고만 생각해왔다. 하지만 이번 주차 학습을 통해 함수형 프로그래밍의 본질과 자바스크립트의 특성을 이해하면서, 클린 코드에 대한 더 깊은 통찰을 얻을 수 있었다.
이번 주차는 배울 것이 많았다. 학습한 내용들을 어떻게 풀어낼지 고민이 되지만, 최선을 다해 정리해보고자 한다.

# 5주차 과제 WIL
## 디자인 패턴
### 디자인 패턴은 왜 어렵게 느껴질까?
디자인 패턴이라고 하면 대부분 앞서 말한 GoF의 23가지 패턴을 떠올린다. Singleton, Factory, Observer...**이런 패턴들은 대부분 Java나 C++ 같은 객체지향 언어를 기반으로 설계**되었다. 하지만 우리가 사용하는 JavaScript는 조금 특별한 언어다

JavaScript는 재미있는 태생을 가지고 있다. JavaScript의 창시자는 처음에 JS를 함수형 언어로 설계하려 했지만, 당시 Java의 인기로 인해 결국 _Java스러운 문법_을 채택하게 되었다고 한다. 그래서 함수형과 객체지향이 묘하게 섞인 언어가 되었고, 전통적인 디자인 패턴을 그대로 적용하기가 쉽지 않게 되어버렸다.

### 알고 보니 우리도 디자인 패턴을 쓰고 있었다
그렇다면 프론트엔드 개발자는 디자인 패턴과 무관할까? 전혀 그렇지 않다. 우리는 이미 다양한 디자인 패턴을 활용하고 있다. 예를 들어보자.

- 컴포넌트를 어떻게 나눌까 고민할 때 -> 컴포넌트 기반 아키텍처 패턴을 사용하고 있음.
- Redux나 MobX로 상태 관리를 할 때 -> 상태 관리 패턴을 적용하고 있음.
- async/await로 API 통신을 처리할 때? -> 비동기 프로그래밍 패턴을 활용하고 있음.
- React.memo()로 성능을 최적화할 때? -> 성능 최적화 패턴을 쓰고 있음.

### 디자인 패턴, 이제는 친숙하게 다가가자
![](https://velog.velcdn.com/images/khy2106/post/04f3c3df-8ee8-492c-b5bf-9a4b8aa0277f/image.png)

결국 디자인 패턴이란 "**이런 상황에서는 이렇게 하면 돼요**"라는 선배 개발자들의 경험이 담긴 해결책이라고 한다. 우리는 이미 이런 패턴들을 사용하고 있었다. 단지 그것을 '디자인 패턴'이라고 인식하지 못했을 뿐.
디자인 패턴에 대한 발제 자료 끝에 이런 내용이 나온다 : 이제 우리가 할 일은 우리가 무의식적으로 사용하고 있던 이 패턴들에 이름을 붙이고, 그 의도와 사용법을 더 깊이 이해하는 것이다. 그렇게 함으로써 우리는 더 나은 프론트엔드 개발자로 성장할 수 있다.

<br />

## 함수형 프로그래밍
### 함수형 프로그래밍, 우리는 왜 배워야 할까?

프론트엔드 개발을 하다 보면 '**상태 관리**'라는 말을 자주(계속) 듣는다. 사용자의 클릭, 입력, API 호출... 이 모든 것들이 애플리케이션의 상태를 변화시키고, 그 변화가 화면에 그려진다. 이런 복잡한 상태 변화를 어떻게 하면 더 예측 가능하고 안정적으로 만들 수 있을까? 여기서 함수형 프로그래밍의 개념에 대한 이해가 필요하다.

### JavaScript와 함수형 프로그래밍

앞서 말했듯 JavaScript가 완벽한 객체지향 언어도, 완벽한 함수형 언어도 아니다. 이 특징은 단점이 될 수도 있지만, 오히려 **두 패러다임의 장점을 적절히 활용**할 수 있는 기회가 된다. 그렇다면 어떻게 하면 이 두 가지를 잘 조화시킬 수 있을까?
![](https://velog.velcdn.com/images/khy2106/post/4f7c2859-2afb-4686-a7e9-a4068c7af817/image.png)


### 함수형 프로그래밍의 핵심: 세 가지 구분하기

함수형 프로그래밍의 핵심은 프로그램을 세 가지 요소로 명확히 구분하는 것이라고 한다:
1. 데이터: 불변의 값
2. 계산: 순수 함수
3. 행동: 부수 효과를 포함한 함수

이렇게 구분하는 이유는 간단하다. **테스트하기 쉬운 코드가 좋은 코드**이기 때문이다. 특히 '변하는 것'은 테스트하기가 어렵다. 그래서 우리는 변화를 최소화하고, 예측 가능한 코드를 만들어야 한다.

### 순수 함수의 조건

테오 코치님이 말씀하신 좋은 함수형 코드를 위한 순수 함수의 조건은 다음과 같다:
- 반환값이 반드시 있어야 한다
- 입력값이 있어야 한다
- 같은 입력에는 항상 같은 출력이 나와야 한다
- 함수 실행이 외부 세계에 영향을 주지 않아야 한다

### 순수 함수만으로 프로그래밍이 가능할까? (아니면 어떻게 하지?)
![](https://velog.velcdn.com/images/khy2106/post/d6999fb2-ba3e-4860-8f7c-73bc24c2d731/image.png)

테오 코치님이 발제 때 소개해 주신 내용. "순수 함수만으로 프로그래밍이 될까요?" useState 같은 상태 관리는? API 호출은? 이렇게 순수 함수가 아닌 것들을 적절히 이용하기 위해서는 어떻게 해야 할까?

해답은 "**행동(부수 효과)을 프로그램의 가장자리로 밀어내는 것**"이라고 한다. 예를 들어:
1. 복잡한 로직과 계산은 순수 함수 체인으로 처리
2. API 호출이나 상태 변경 같은 부수 효과는 프로그램의 시작점과 끝점에 위치시
3. 데이터는 순수 함수들을 거쳐 변환되는 흐름으로 구성
4. 부수 효과는 가능한 한 늦게 실행

### 결론: 함수형 프로그래밍을 이해하면, 좋은 코드를 짤 수 있다.

함수형 프로그래밍은 어려운 개념이 아니다. 우리의 코드를 더 예측 가능하고, 테스트하기 쉽고, 유지보수하기 좋게 만드는 **일종의 방법**이라고 할 수 있다. 완벽한 함수형 코드를 쓰는 것이 목표가 아니라, 함수형 사고를 통해 더 나은 코드를 작성하는 것이 진정한 목표가 될 것이다.

<br />

## 5주차 과제 진행 후기


5주차 기본 과제의 내용은 다음과 같다. 

![](https://velog.velcdn.com/images/khy2106/post/bdfe11af-c328-49ae-8777-446ab14a4701/image.png)

심화 과제의 경우, 직접 컴포넌트, 커스텀 훅, 유틸 함수를 리팩토링하고 이를 기반으로 테스트 코드를 작성하는 것이었다. 

배운 내용을 바로 실전에 적용하려고 노력했지만, 현실은 그리 녹록지 않았다. 특히 시간적 제약 속에서 UI와 로직을 분리하고, 비즈니스 로직이 포함된 부분과 그렇지 않은 부분을 구분하는 작업은 생각보다 큰 도전이었다. 이론과 실제 적용 사이의 간극을 체감하는 순간이었달까.


#### 로직을 Custom Hook으로 분리하기
```ts
// before
export const CartPage = ({ products, coupons }: Props) => {
  const [cart, setCart] = useState<CartItem[]>([]);
  const [selectedCoupon, setSelectedCoupon] = useState<Coupon | null>(null);


  const addToCart = (product: Product) => {
    const remainingStock = getRemainingStock(product);
    if (remainingStock <= 0) return;

    setCart(prevCart => {
      const existingItem = prevCart.find(item => item.product.id === product.id);
      if (existingItem) {
        return prevCart.map(item =>
          item.product.id === product.id
            ? { ...item, quantity: Math.min(item.quantity + 1, product.stock) }
            : item
        );
      }
      return [...prevCart, { product, quantity: 1 }];
    });
  };

  const removeFromCart = (productId: string) => {
    setCart(prevCart => prevCart.filter(item => item.product.id !== productId));
  };

  const updateQuantity = (productId: string, newQuantity: number) => {
    setCart(prevCart =>
      prevCart.map(item => {
        if (item.product.id === productId) {
          const maxQuantity = item.product.stock;
          const updatedQuantity = Math.max(0, Math.min(newQuantity, maxQuantity));
          return updatedQuantity > 0 ? { ...item, quantity: updatedQuantity } : null;
        }
        return item;
      }).filter((item): item is CartItem => item !== null)
    );
  };

  const calculateTotal = () => {
    let totalBeforeDiscount = 0;
    let totalAfterDiscount = 0;

    cart.forEach(item => {
      const { price } = item.product;
      const { quantity } = item;
      totalBeforeDiscount += price * quantity;

      const discount = item.product.discounts.reduce((maxDiscount, d) => {
        return quantity >= d.quantity && d.rate > maxDiscount ? d.rate : maxDiscount;
      }, 0);

      totalAfterDiscount += price * quantity * (1 - discount);
    });

    let totalDiscount = totalBeforeDiscount - totalAfterDiscount;

    // 쿠폰 적용
    if (selectedCoupon) {
      if (selectedCoupon.discountType === 'amount') {
        totalAfterDiscount = Math.max(0, totalAfterDiscount - selectedCoupon.discountValue);
      } else {
        totalAfterDiscount *= (1 - selectedCoupon.discountValue / 100);
      }
      totalDiscount = totalBeforeDiscount - totalAfterDiscount;
    }

    return {
      totalBeforeDiscount: Math.round(totalBeforeDiscount),
      totalAfterDiscount: Math.round(totalAfterDiscount),
      totalDiscount: Math.round(totalDiscount)
    };
  };


  const getMaxDiscount = (discounts: { quantity: number; rate: number }[]) => {
    return discounts.reduce((max, discount) => Math.max(max, discount.rate), 0);
  };

  const getRemainingStock = (product: Product) => {
    const cartItem = cart.find(item => item.product.id === product.id);
    return product.stock - (cartItem?.quantity || 0);
  };

  const { totalBeforeDiscount, totalAfterDiscount, totalDiscount } = calculateTotal()

  const getAppliedDiscount = (item: CartItem) => {
    const { discounts } = item.product;
    const { quantity } = item;
    let appliedDiscount = 0;
    for (const discount of discounts) {
      if (quantity >= discount.quantity) {
        appliedDiscount = Math.max(appliedDiscount, discount.rate);
      }
    }
    return appliedDiscount;
  };

  const applyCoupon = (coupon: Coupon) => {
    setSelectedCoupon(coupon);
  };

  return (
    <div className="container mx-auto p-4">
      <h1 className="text-3xl font-bold mb-6">장바구니</h1>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
        <div>
   // ...JSX 계속
```

 
    
```ts
// after
import { useState } from "react";
import { CartItem, Coupon, Product } from "../../types";
import { calculateCartTotal, updateCartItemQuantity } from "../models/cart";

export const useCart = () => {
  const [cart, setCart] = useState<CartItem[]>([]);
  const [selectedCoupon, setSelectedCoupon] = useState<Coupon | null>(null);

  const addToCart = (product: Product) => {
    const existingItem = cart.find((item) => item.product.id === product.id);
    const cartItem = cart.find((item) => item.product.id === product.id);
    const currentQuantity = cartItem?.quantity || 0;

    if (currentQuantity >= product.stock) return;

    if (existingItem) {
      setCart((prev) =>
        updateCartItemQuantity(prev, product.id, existingItem.quantity + 1)
      );
    } else {
      setCart((prev) => [...prev, { product, quantity: 1 }]);
    }
  };

  const removeFromCart = (productId: string) => {
    setCart((prev) => prev.filter((item) => item.product.id !== productId));
  };

  const updateQuantity = (productId: string, newQuantity: number) => {
    setCart((prev) => updateCartItemQuantity(prev, productId, newQuantity));
  };

  const applyCoupon = (coupon: Coupon) => {
    setSelectedCoupon(coupon);
  };

  const calculateTotal = () => calculateCartTotal(cart, selectedCoupon);

  return {
    cart,
    addToCart,
    removeFromCart,
    updateQuantity,
    applyCoupon,
    calculateTotal,
    selectedCoupon,
  };
};
```
Custom Hook을 만들면서 가장 큰 고민이 찾아왔다. Cart 관련 로직들을 useCart.ts로 분리하는 과정에서 던진 질문은 이거였다.

**"정말 이 로직들이 'Cart'라는 이유만으로 한데 묶여도 될까?"**

단순히 '장바구니'와 관련됐다는 이유로 모든 로직을 하나의 Custom Hook으로 모으는 게 맞는 걸까... 아니면 더 세분화된 기준이 필요한 걸까? 예를 들어:

- 장바구니 상품 목록을 관리하는 로직
- 장바구니 총액을 계산하는 로직
- 장바구니 상품 수량을 변경하는 로직

이런 고민은 결국 "**관심사를 분리하는 기준을 어떻게 잡을 것인가**"라는 더 근본적인 질문으로도 이어졌다. (그리고 이건 6주차 주제라는 사실...두둥) 단순히 '연관됐다'는 이유만으로 코드를 묶는 게 아니라, 각 로직의 책임과 역할에 따라 더 섬세하게 분리해야 하지 않을까?

앞으로 이런 기준을 더 명확하게 정립해 나가야겠다는 숙제를 안게 되었다. 코드를 분리한다는 건, 단순히 파일을 나누는 게 아니라 각 부분의 책임과 역할을 명확히 하는 과정이니까.


### 비즈니스 로직이 도대체 뭔데
프론트엔드 개발을 하다 보면 수많은 함수들을 작성하게 된다. 그 과정에서 '이 함수는 비즈니스 로직일까, 아닐까?' 하는 고민이 끊임없이 찾아온다. 나 역시 이번 과제를 진행하면서 이 구분에 적잖은 혼란을 겪었다.

비즈니스 로직의 정의는 의외로 단순하다. "**우리 서비스가 어떻게 동작해야 하는가**"에 대한 규칙들이 바로 비즈니스 로직이다. 반면에 데이터를 어떻게 보여줄지, 어떻게 저장할지와 같은 '**구현**'에 관한 부분은 비즈니스 로직이 아니다.

```ts

// 쿠폰 할인 적용
export const applyCouponDiscount = (
  amount: number,
  coupon: Coupon | null
): number => {
  if (!coupon) return amount;

  if (coupon.discountType === "amount") {
    return Math.max(0, amount - coupon.discountValue);
  }

  return amount * (1 - coupon.discountValue / 100);
};

// 쿠폰 할인 표시 형식
export const formatCouponDisplay = (coupon: Coupon) => {
  return coupon.discountType === "amount"
    ? ${coupon.discountValue}원
    : ${coupon.discountValue}%;
};
```
`applyCouponDiscount` 함수는 할인 계산 방식이라는 비즈니스 규칙을 담고 있다. 반면 `formatCouponDisplay` 함수는 단순히 할인 정보를 어떻게 보여줄지에 대한 표현 방식을 다룬다.

처음에는 이 구분이 어려워서, 함수의 파라미터에 엔티티가 포함되어 있는지를 기준으로 삼았다. 엔티티가 있으면 비즈니스 로직, 없으면 단순 공통 함수로 분류했던 것이다. 하지만 이는 너무 단순한 접근이었다. (바보)

이제는 더 명확한 기준이 생겼다. "이 함수가 서비스의 동작 규칙을 담고 있는가, 아니면 단순히 구현 방식을 다루고 있는가?"를 살펴보면 된다. 이 기준을 통해 앞으로는 더 체계적으로 코드를 구조화할 수 있을 것 같다.


# 마무리하면서

이번 주차 발제는 나에게 큰 깨달음의 시간이었다. 그동안 막연하게 이해하고 있던 개념들과, JavaScript로 개발하면서 품었던 여러 의문점들이 명쾌하게 해소되는 순간이었기 때문이다.

가장 놀라웠던 발견은 **JavaScript의 정체성**에 대한 것이었다. JavaScript가 객체 지향과 함수형이 절묘하게 섞인 '혼종' 언어라는 사실은 정말 신선한 충격이었다. 이런 기본적인 사실도 모르고 있었다니... 개발 서적을 더 열심히 읽지 않은 것이 살짝 후회되는 순간이었다. 그래서 바로 실천에 옮겼다. 함수형 프로그래밍 입문서의 정석이라는 '쏙쏙 들어오는 함수형 코딩'을 질러 버렸다.
![](https://velog.velcdn.com/images/khy2106/post/89b825e0-a0f9-43cd-98fc-5d2c086acc75/image.png)


이러한 배경지식은 실제 개발 과정에서 마주치는 여러 고민들을 해결하는 데 큰 도움이 되었다. 특히 디자인 패턴을 적용할 때나 코드를 구조화할 때, JavaScript의 이중적 특성을 이해하고 있다는 것이 큰 차이를 만들어냈다. 예를 들어, 비즈니스 로직을 분리할 때도 단순히 코드를 나누는 것이 아니라, **각 부분의 책임과 역할에 따라 더 섬세하게 접근**해볼 수 있게 되었다.

또한 함수형 프로그래밍의 관점에서 보니, 기존에 작성하던 코드들이 새롭게 보이기 시작했다. **'순수 함수'와 '부수 효과'** 라는 렌즈를 통해 코드를 바라보니, 어떤 부분을 개선해야 할지가 더 명확하게 보였다. 특히 상태 관리나 비동기 처리와 같은 복잡한 로직을 다룰 때, 함수형 프로그래밍의 원칙들이 훌륭한 가이드라인이 되어주었다.

이제는 단순히 '작동하는 코드'를 넘어서, '더 나은 코드'를 작성하기 위한 이론적 기반을 갖추게 된 것 같다. 앞으로도 이러한 학습을 통해, JavaScript를 더 깊이 이해하고 효과적으로 활용할 수 있는 개발자로 성장해나가고 싶다.
