# Week 1


## 라우터 구현하기

전체 코드
```js
// router.js

import { MainPage } from "../pages/MainPage.js";
import { ProfilePage } from "../pages/ProfilePage.js";
import { LoginPage } from "../pages/LoginPage.js";
import { ErrorPage } from "../pages/ErrorPage.js";

const routes = {
 "/": MainPage,
 "/profile": ProfilePage, 
 "/login": LoginPage,
 "/404": ErrorPage,
};

const routerTypes = {
 history: {
   getPath: () => window.location.pathname,

   updateURL: (url, { replace = false } = {}) => {
     const pathname = url.startsWith("http") ? new URL(url).pathname : url;

     if (replace) {
       history.replaceState(null, null, pathname);
     } else {
       history.pushState(null, null, pathname);
     }
     return pathname;
   },

   setupListeners: (handleRoute) => {
     const popstateHandler = () => handleRoute(routerTypes.history.getPath());
     window.removeEventListener("popstate", popstateHandler);
     window.addEventListener("popstate", popstateHandler);

     const clickHandler = (e) => {
       const link = e.target.closest("[data-link]");
       if (link) {
         e.preventDefault();
         const path = routerTypes.history.updateURL(link.href);
         handleRoute(path);
       }
     };
     document.removeEventListener("click", clickHandler);
     document.addEventListener("click", clickHandler);
   },
 },
 hash: {
   getPath: () => {
     const hash = window.location.hash.replace(/^#/, "");
     return hash ? hash : "/";
   },

   updateURL: (url, { replace = false } = {}) => {
     const hashPath = url.startsWith("http")
       ? new URL(url).hash.replace(/^#/, "")
       : url.replace(/^#/, "");
     const targetHash = hashPath.startsWith("/") ? hashPath : `/${hashPath}`;

     if (replace) {
       const currentURL = new URL(window.location.href);
       currentURL.hash = targetHash;
       history.replaceState(null, null, currentURL.href);
     } else {
       window.location.hash = targetHash;
     }

     return targetHash;
   },

   setupListeners: (handleRoute) => {
     const hashChangeHandler = () => handleRoute(routerTypes.hash.getPath());
     window.removeEventListener("hashchange", hashChangeHandler);
     window.addEventListener("hashchange", hashChangeHandler);

     const clickHandler = (e) => {
       const link = e.target.closest("[data-link]");
       if (link) {
         e.preventDefault();
         const href = link.getAttribute("href");
         const path = routerTypes.hash.updateURL(href);
         handleRoute(path);
       }
     };
     document.removeEventListener("click", clickHandler);
     document.addEventListener("click", clickHandler);
   },
 },
};

const createRouter = (type = "history") => {
 const router = routerTypes[type];
 let rootElement;

 const renderPage = (path) => {
   const page = routes[path] || routes["/404"];
   if (!page) return;
   rootElement.innerHTML = page();
 };

 const handleRoute = (path) => {
   const user = JSON.parse(localStorage.getItem("user"));

   if (path === "/profile" && !user) {
     navigate("/login", { replace: true });
     return;
   }

   if (path === "/login" && user) {
     navigate("/", { replace: true });
     return;
   }

   renderPage(path);
 };

 const navigate = (url, options) => {
   const path = router.updateURL(url, options);
   handleRoute(path);
   return path;
 };

 const init = (rootElementId) => {
   rootElement = document.getElementById(rootElementId);
   if (!rootElement) return;

   router.setupListeners(handleRoute);
   handleRoute(router.getPath());
 };

 return { init, navigate };
};

export default createRouter;
```


## 1. Class형 라우터 vs 함수형 라우터
### 클래스형 라우터
클래스형 라우터의 가장 큰 특징은 **객체지향적 설계**와 **상속을 통한 확장성**이다. 

`this` 키워드를 통해 상태를 관리하고, 메서드를 논리적으로 그룹화할 수 있다. 

특히 기본 라우터를 확장하여 해시 라우터를 구현할 때 상속의 이점이 두드러진다.


### 함수형 라우터
함수형 라우터는 **클로저를 활용한 상태 관리**와 **함수 단위의 모듈화**가 특징이다. 

코드 구조가 더 **직관적이고 이해하기 쉬우며**, 가벼운 프로그래밍에 적합하다. 

특히 주니어 개발자의 관점에서는 클래스의 this 바인딩 이슈를 신경 쓰지 않아도 되는 장점이 있다.

클래스형의 경우 상속을 통한 코드 재사용성이 장점이지만, 때로는 이 상속 구조가 오히려 복잡성을 가중시킬 수 있다. 

반면 함수형은 단순하고 예측 가능한 코드 흐름을 만들 수 있지만, 복잡한 객체 관계를 표현하기에는 제한적일 수 있다.

<br />

라우터를 구현하기에 앞서, 클래스 문법을 사용할지 함수형으로 접근할지 깊이 고민했다. 

클래스는 아직 익숙하지 않은 영역이었기에, 결국 함수형 구현 방식을 선택했다.

클래스를 활용했다면 객체 지향 프로그래밍을 실습하고 이해하는 좋은 기회가 되었겠지만, 나는 다른 접근을 택했다.

익숙한 함수형 프로그래밍을 바탕으로, 라우터의 핵심을 이해하고 확장 가능한 구조를 설계하는 데 더 집중하기로 한 것이다.



## 2. History API 기반 라우터와 Hash 라우터의 차이
예시

```js
// History API 라우터
const historyRouter = {
  getPath: () => window.location.pathname,
  updateURL: (url) => {
    history.pushState(null, null, url);
  },
  setupListeners: (handler) => {
    window.addEventListener("popstate", handler);
  }
};

// Hash 라우터
const hashRouter = {
  getPath: () => window.location.hash.replace(/^#/, ""),
  updateURL: (url) => {
    window.location.hash = url;
  },
  setupListeners: (handler) => {
    window.addEventListener("hashchange", handler);
  }
};
```


### History API 라우터
History API 라우터는 브라우저의 History API를 활용하여 실제 URL을 조작한다. `/profile`, `/about`과 같은 일반적인 URL 경로를 사용하며, 이는 실제 서버의 경로처럼 작동한다. 따라서 서버 사이드에서도 이러한 URL에 대한 적절한 처리가 필요하다.

특히 새로고침을 했을 때 해당 URL로 서버에 실제 요청이 가기 때문에, 서버에서 모든 라우트에 대해 같은 HTML 파일을 반환하도록 설정해야 한다. 그렇지 않으면 404 에러가 발생할 수 있다. 

대신 URL이 깔끔하고 SEO에 더 유리하다는 장점이 있다.

### Hash 라우터
Hash 라우터는 URL의 해시(#) 부분을 활용한다. `/#/profile`, `/#/about`처럼 #(해시) 뒤에 경로를 표시하는데, 중요한 점은 해시 이후의 부분은 서버로 전송되지 않는다는 것이다. 

브라우저는 해시 이전의 부분만 서버로 요청을 보낸다.

이러한 특성 때문에 Hash 라우터는 서버 설정 없이도 SPA 라우팅이 가능하다. 

새로고침을 하더라도 서버는 항상 기본 HTML 파일만 응답하면 되고, 클라이언트에서 해시 값을 기반으로 라우팅을 처리한다. 단, URL이 다소 지저분해 보이고 SEO에는 불리할 수 있다는 단점이 있다고 한다.

## 3. History API의 주요 메서드들

예시
```js
// 페이지 이동 시
const navigateToProfile = () => {
  history.pushState(null, '', '/profile');  // 히스토리에 추가
  renderProfile();  // 페이지 렌더링
}

// 로그인 리다이렉트 시
const redirectToLogin = () => {
  history.replaceState(null, '', '/login');  // 히스토리 교체
  renderLogin();  // 페이지 렌더링
}

// 뒤로가기 처리
window.addEventListener('popstate', () => {
  const currentPath = window.location.pathname;
  handleRoute(currentPath);  // 현재 경로에 맞는 페이지 렌더링
});
```

### 1. pushState
```js
history.pushState(state, title, url)
```

 새로운 히스토리 엔트리를 추가한다.
- 브라우저의 뒤로 가기를 사용할 수 있게 된다.
- URL은 변경되지만 페이지는 새로고침되지 않는다.
1. 일반적인 페이지 네비게이션
2. 사용자가 뒤로가기로 돌아올 수 있어야 하는 경우

### 2. replaceState
```js
history.replaceState(state, title, url)
```
현재 히스토리 엔트리를 새것으로 교체한다.
- 브라우저의 뒤로 가기 히스토리에 추가되지 않는다.
- URL은 변경되지만 페이지는 새로고침되지 않는다.

1. 리다이렉션
2. 에러 페이지 전환
3. 로그인/로그아웃 처리

### 3. popstate 이벤트
```js
window.addEventListener('popstate', (event) => {
  // 뒤로가기/앞으로가기 시 실행될 코드
})
```
- 브라우저의 뒤로가기/앞으로가기 버튼 클릭 시 발생한다.
- pushState/replaceState로는 발생하지 않음


1. 발생 조건:
  - 브라우저 뒤로가기/앞으로가기 버튼 클릭
  - history.back() / history.forward() / history.go() 호출
2. 발생하지 않는 경우:
  - pushState() 호출
  - replaceState() 호출
  - 일반적인 링크 클릭
  
```js
window.addEventListener('popstate', (event) => {
  console.log(event.state);  // pushState/replaceState에서 전달한 state 객체
});
```

이전에 pushState()나 replaceState()를 통해 저장해둔 state 객체를 popstate 이벤트가 발생할 때 event.state로 받아볼 수 있다.


# 마무리하면서

1주차 과제를 진행하면서 매일 새벽 3시까지 불태우는 날들이 이어졌다. 

연말이라 각종 모임과 친구들과의 약속까지 겹쳐서 (진작 알았다면 약속을 안 잡았을 텐데...) 정말 정신없는 한 주를 보냈다.


첫 과제는 분명 고된 여정이었지만, 이를 통해 내가 꿈꾸던 진정한 개발자의 모습에 한 걸음 더 가까워진 것 같아 뿌듯하다.

내일부터 시작되는 2주차도 이 기세를 이어가보려 한다.

팀원들하고도 더욱 친해지고 싶다.

![](https://velog.velcdn.com/images/khy2106/post/2bd3fda8-73f3-4197-9423-52922eb4aa01/image.png)



파이팅!
    