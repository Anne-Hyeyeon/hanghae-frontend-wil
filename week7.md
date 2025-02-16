# 들어가면서

항해 플러스 프론트엔드 과정의 chapter 3에서는 프론트엔드 개발자들 사이에서 화두가 되고 있는 **프론트엔드 테스트 코드**를 다루게 된다.


개발자로서 경력을 쌓기 시작했을 때부터 테스트 코드는 늘 내게 부담스러운 주제였다. **"이직을 위해서는 테스트 코드가 필수"** 라는 말을 자주 들었지만, 실무나 사이드 프로젝트에서 직접 다뤄본 경험이 없어 막연한 두려움만 가지고 있었기 때문이다.

하지만 항해 플러스 7~8주차 과정을 통해 프론트엔드 테스트의 정의부터 종류, 라이브러리, 그리고 실무 적용 방안까지 체계적으로 배울 수 있었다. 이론과 실습을 통해 테스트에 대한 막연한 두려움을 극복하고, 실제 개발 환경에 적용할 수 있는 자신감을 얻게 되었다. (...는 아직도 좀 모자라다 ㅠㅠ)

이번 포스팅에서는 프론트엔드 테스트의 일반적인 내용보다는, **실무에서 즉시 활용 가능한 MSW(Mock Service Worker)에 대해 집중적으로 다뤄보려 한다.** 

MSW는 API Mocking 라이브러리로, 실제 네트워크 요청을 가로채어 모의 응답을 제공한다. 이를 통해 백엔드 API가 완성되기 전에도 프론트엔드 개발을 진행할 수 있으며, vitest 등을 활용한 테스트 작성도 수월해진다.

아래에서는 MSW를 Next.js 프로젝트에 설치하고 실제로 적용하는 과정을 상세히 다뤄볼 예정이다.
<br />
<hr />


# 내가 춘자라니에 MSW 적용하기

## UI 구성

내가 작년에 만든 [내가 춘자라니](https://chunja.vercel.app/) 웹사이트는 영어 이름의 느낌을 알려주는 서비스를 제공하고 있는데, 여기에 재미있는 기능을 하나 추가하면 어떨까 생각해보았다. 

바로** 영어 이름 궁합 기능**이다. (실제로 구현을 고려 중인 아이디어다.)

아직 구체적인 도입 계획은 없지만, 이 기능을 추가한다고 가정하고 Cursor에게 화면 구성을 의뢰해보았다.

![](https://velog.velcdn.com/images/khy2106/post/58255aed-379d-4af8-b41b-3119e6688b53/image.png)

![](https://velog.velcdn.com/images/khy2106/post/3fac7731-e9be-4b4b-9a1f-08102a1d5402/image.png)


Cursor는 이렇게 꽤 그럴듯한 UI를 만들어주었다. (만쉐) 

결과 페이지에서 보여줄 **궁합 점수와 스토리**는 서버 API를 통해 받아올 예정인데, 

이 부분의 개발을 위해 MSW(Mock Service Worker)를 활용해보려 한다.

<br />

## MSW 설치

프로젝트에 다음 명령어로 msw와 테스팅 라이브러리 패키지를 설치해 준다.

(명령어는 발제 자료를 참고했다.)

```
# 필요한 패키지 설치
$ npm install axios msw @testing-library/react @testing-library/jest-dom @testing-library/user-event vitest jsdom --save-dev

# MSW 서비스 워커 파일 생성
$ npx msw init public/ --save
```

![](https://velog.velcdn.com/images/khy2106/post/d74d8a4c-4242-4aef-9c2a-1f706383f7ff/image.png)

그럼 요렇게 public 폴더에 mockServiceWorker.js라는 파일이 설치된 걸 확인할 수 있다.


### mockServiceWorker.js란?

`mockServiceWorker.js`는 MSW의 핵심 파일이다.  브라우저에서 일어나는 네트워크 요청을 가로채서 우리가 설정한 모의 응답을 대신 보내주는 역할을 한다.

실제로 MSW를 프로젝트에 도입할 때는 이 파일의 위치가 매우 중요하다. mockServiceWorker.js가 올바른 위치에 있어야만 API 모킹이 제대로 동작하기 때문이다. 

<br />


## next.config.js 파일 수정하기

Next.js 프로젝트에서 MSW를 이용하기 위해서는 next.config.js 파일 수정이 필요하다. 

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  webpack: (config, context) => {
    // 서버 빌드인 경우
    if (context?.isServer) {
      // msw/browser를 무시하도록 설정
      if (Array.isArray(config.resolve.alias)) { 
        // Next.js에서는 항상 객체 타입이지만,
        // TypeScript 오류를 피하고 
        // 향후 발생할 수 있는 변경사항에 대비하기 위해 이 분기를 유지
        config.resolve.alias.push({ name: "msw/browser", alias: false })
      } else {
        config.resolve.alias["msw/browser"] = false
      }
    } 
    // 브라우저 빌드인 경우
    else {
      // msw/node를 무시하도록 설정
      if (Array.isArray(config.resolve.alias)) {
        config.resolve.alias.push({ name: "msw/node", alias: false })
      } else {
        config.resolve.alias["msw/node"] = false
      }
    }
    return config
  },
};

export default nextConfig;
```

이 설정은 Next.js에서 MSW를 사용할 때 서버와 브라우저 환경을 명확히 구분하기 위한 웹팩 설정이라고 한다.

_서버에서 빌드할 때는 msw/browser 모듈을 무시 (브라우저 전용 코드이므로)
브라우저용으로 빌드할 때는 msw/node 모듈을 무시 (서버 전용 코드이므로)_


<br />


## package.json에 명령어 추가하기
package.json의 script에 명령어를 추가한다.
```
  "scripts": {
    "postinstall": "npx msw init public/ --save"
	}
```

### postinstall이란?

npm이나 yarn으로 패키지를 설치(npm install)한 직후에 자동으로 실행되는 스크립트.

즉, 누군가 프로젝트를 클론하고 npm install을 실행하면, 모든 패키지 설치가 끝난 후 이 스크립트가 자동으로 실행된다는 뜻이다.

<br />


## mockApiHandler.ts 

다음으로 src 폴더에 `mockApiHandler.ts` 파일을 생성해보자. 이 파일은 MSW의 핵심이라고 할 수 있는데, 실제 API 요청을 가로채서 어떤 응답을 보내줄지 정의하는 파일이기 때문이다.

예를 들어 우리가 서버에 GET 요청을 보내면, 서버는 데이터를 담아서 응답을 보내줄 것이다. 그리고 mockApiHandler.ts는 이런 과정을 흉내내는 역할을 한다. **서버가 아직 준비되지 않았더라도, 이 파일에서 정의한 대로 응답이 오기 때문에 프론트엔드 개발을 진행할 수 있다.**

아래는 내가 춘자라니 - 이름 궁합 기능에 필요한 핸들러를 구현한 예시다. 결과값을 서버에서 받아와 보여주는 단순한 컴포넌트인만큼 get 요청만을 모킹했다.

```ts
import { http, HttpResponse } from 'msw'

interface StoryTemplate {
  location: string;
  situation: string;
  action: string;
  emoji: string;
}

const storyTemplates: StoryTemplate[] = [
    {
        location: "몬타나 목장",
        situation: "함께 말을 키우던",
        action: "마구간에서 뜨겁게 키스를 나눠요",
        emoji: "🤠💕"
      },
      {
        location: "뉴욕 센트럴파크",
        situation: "공원 카페에서 마주친",
        action: "베이글을 나눠 먹으며 사랑을 속삭여요",
        emoji: "🗽💝"
      },
      {
        location: "뉴올리언스",
        situation: "미시시피강 유람선을 타고 건너던",
        action: "달빛 아래서 로맨틱한 세레나데를 불러요",
        emoji: "🚣‍♂️✨"
      },
      {
        location: "워싱턴 DC",
        situation: "벚꽃 축제에서 마주친",
        action: "함께 축제를 즐기며 로맨틱한 시간을 보내요",
        emoji: "🌸💮"
      },
      {
        location: "캘리포니아 나파밸리",
        situation: "포도밭을 바라보며 와인을 마시던",
        action: "와이너리 테라스에서 로맨틱한 춤을 춰요",
        emoji: "🍷✨"
      },
      {
        location: "콜로라도 로키산맥",
        situation: "스키 리조트에서 우연히 만난",
        action: "따뜻한 산장에서 핫초코를 나눠 마셔요",
        emoji: "⛷️❄️"
      }
];

const getCompatibilityMessage = (score: number): string => {
  if (score >= 90) return "운명적인 만남이에요! 💘";
  if (score >= 80) return "아주 좋은 궁합이에요! 💝";
  if (score >= 70) return "잘 어울리는 조합이에요! 💖";
  if (score >= 60) return "나쁘지 않은 궁합이에요! 💗";
  return "서로를 이해하려 노력한다면 좋아질 거예요! 💓";
};

const getRandomStory = (): StoryTemplate => {
  return storyTemplates[Math.floor(Math.random() * storyTemplates.length)];
};

export const mockApiHandlers = [
  http.get('/api/compatibility', ({ request }) => {
    const url = new URL(request.url);
    const name1 = url.searchParams.get('name1');
    const name2 = url.searchParams.get('name2');
    
    if (!name1 || !name2) {
      return new HttpResponse(null, {
        status: 400,
        statusText: '이름 파라미터가 필요합니다.'
      });
    }
    
    const score = Math.floor(Math.random() * 51) + 50;
    const story = getRandomStory();
    const message = getCompatibilityMessage(score);
    
    const storyText = `${decodeURIComponent(String(name1))}님과 ${decodeURIComponent(String(name2))}님이 미국에서 만났다면...?\n${story.location}에서 ${story.situation} 사이!\n${story.action} ${story.emoji}`;
    
    return HttpResponse.json({
      score,
      message,
      story: storyText,
      timestamp: new Date().toISOString()
    });
  })
];

export default mockApiHandlers;

```
여기서 템플릿 모킹 데이터는 별도의 파일로 만들어도 깔끔할 듯하다. 예시에서는 편의성을 위해 한 파일에 때려박았다..!


<br />

## browser.ts 파일 생성하기
src에 mocks 폴더를 만들고 broswer.ts를 만들어 다음과 같이 worker를 만들어 준다.

```ts
import { mockApiHandlers } from '@/mockApiHandlers'
import { setupWorker } from 'msw/browser'

export const worker = setupWorker(...mockApiHandlers)
```

React로만 된 프로젝트와 다르게 Next.js에서 별도로 browser.ts를 만드는 이유는 Next.js에서 별도의 browser.ts를 만드는 이유는 서버 사이드 렌더링(SSR)과 클라이언트 사이드의 동작 방식 차이 때문이다.

Next.js의 특징을 생각해보면: 서버에서 먼저 렌더링하고 (SSR) 그 다음 브라우저에서 실행된다. (Client-side)

근데 MSW는? MSW는 두 가지 모드가 있다.

- 브라우저용 (Service Worker 사용)
- 서버용 (Node.js 환경용)

그래서 browser.ts가 필요하다. browser.ts 파일은 "얘는 브라우저에서만 실행할 거야!"라고 명확하게 알려주는 역할을 하는 것이다.

위에 설정한 next.config.js랑 역할이 비슷해서 헷갈렸는데, next.config.js의 경우 빌드 시점에서 어떤 MSW 모듈을 포함/제외할 지 결정하고, browswer.ts는 실제 MSW를 초기화하고 실행하는 코드다.

<br />


## MSWInitializer 생성 후 layout.tsx 에 연결
컴포넌트 폴더 (위치는 상관없이 적절한 프로젝트 경로에)에 MSW 초기화를 위한 이니셜라이저를 만들어 준다.

```tsx
// components/MSWInitializer.tsx
'use client'

import { useEffect } from 'react';

export default function MSWInitializer() {
  useEffect(() => {
    if (process.env.NODE_ENV === 'development') { // 개발 모드에서만 동작
      require('../../mocks/browser')
        .worker.start({
          onUnhandledRequest: 'bypass', // 처리되지 않은 요청은 무시
        })
        .catch((error: Error) => {
          console.error('MSW 초기화 실패:', error);
        });
    }
  }, []);

  return null;
}
```

만든 MSW Initializer를 최상단 layout.tsx에서 불러온다.

```tsx
// layout.tsx

import Footer from "./components/layout/Footer";
import CoupangBanner from "./components/common/CoopangBanner";
import styles from "./layout.module.css";
import GoogleAnalyticsScript from "./components/scripts/GoogleAnalyticsScript";
import MSWInitializer from "./components/MSWInitializer";
import "./globals.css";


export default function RootLayout({
 children,
}: {
 children: React.ReactNode;
}) {
 return (
  <html lang="ko">
   <head>
    <GoogleAnalyticsScript />
   </head>
   <body className={styles.body}>
 	<MSWInitializer />
    <main className={styles.main}>{children}</main>
    <Footer />
    <div className={styles.banner}>
     <CoupangBanner />
    </div>
   </body>
  </html>
 );
}
```

<br />


## 컴포넌트 코드

이제 실제로 MSW로 모킹한 API를 사용하는 ChemistryResult.tsx 컴포넌트를 살펴보자.
이 컴포넌트는 두 사람의 이름을 받아 궁합 점수와 스토리를 보여주는 역할을 한다.

```tsx
"use client";

import { useParams } from "next/navigation";
import { useState, useEffect } from "react";
import styles from "./ChemistryResult.module.css";

interface ApiResponse {
  score: number;
  message: string;
  story: string;
}

export default function ChemistryResult() {
  const params = useParams();
  const [compatibilityScore, setCompatibilityScore] = useState<number>(0);
  const [compatibilityMessage, setCompatibilityMessage] = useState<string>('');
  const [compatibilityStory, setCompatibilityStory] = useState<string>('');
  const [loading, setLoading] = useState(true);
  const { name1, name2 } = params;

  useEffect(() => {
    const calculateCompatibility = async () => {
      setLoading(true);
      try {
        const response = await fetch(`/api/compatibility?name1=${name1}&name2=${name2}`);
        const data: ApiResponse = await response.json();
        setCompatibilityScore(data.score);
        setCompatibilityMessage(data.message);
        setCompatibilityStory(data.story);
      } catch (error) {
        console.error('호환성 점수 가져오기 실패:', error);
        setCompatibilityScore(75);
      } finally {
        setLoading(false);
      }
    };

    calculateCompatibility();
  }, [name1, name2]);

  const getHeartAnimation = (score: number) => {
    if (score >= 90) return styles.perfectMatch;
    if (score >= 80) return styles.greatMatch;
    if (score >= 70) return styles.goodMatch;
    if (score >= 60) return styles.okayMatch;
    return styles.normalMatch;
  };

  return (
    <div className={styles.container}>
      <h1 className={styles.title}>궁합 결과</h1>
      
      {loading ? (
        <div className={styles.loadingContainer}>
          <div className={styles.loadingHeart}>💝</div>
          <p>궁합을 분석하고 있어요...</p>
        </div>
      ) : (
        <>
          <div className={styles.namesContainer}>
            <span className={styles.name}>{decodeURIComponent(String(name1))}</span>
            <div className={`${styles.heart} ${getHeartAnimation(compatibilityScore)}`}>
              ❤️
            </div>
            <span className={styles.name}>{decodeURIComponent(String(name2))}</span>
          </div>

          <div className={styles.resultContainer}>
            <div className={styles.scoreContainer}>
              <div className={styles.scoreCircle}>
                <span className={styles.score}>{compatibilityScore}</span>
                <span className={styles.scoreLabel}>점</span>
              </div>
            </div>

            <p className={styles.message}>
              {compatibilityMessage}
            </p>

            <div className={styles.storyContainer}>
              <p className={styles.storyText}>
                {compatibilityStory.split('\n').map((line, index) => (
                  <span key={index}>
                    {line}
                    <br />
                  </span>
                ))}
              </p>
            </div>
          </div>

          <button
            onClick={() => window.location.href = '/chemistry'}
            className={styles.backButton}
          >
            다시 확인하기 🔄
          </button>
        </>
      )}
    </div>
  );
} 
```

<br />


## 테스트 코드에 적용
MSW로 만든 Mock API는 **테스트 코드**에서도 활용할 수 있다. MSW를 테스트 코드에서 쓰려면 서버 세팅부터 해줘야 한다. 아래는 테스트 파일 상단에 들어갈 기본 세팅이다:


```ts
// 테스트에 필요한 라이브러리들 import
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import App from './App.tsx'
import { afterAll, beforeAll, beforeEach, describe, expect, test } from 'vitest'

// MSW 서버 세팅에 필요한 것들 import
import { setupServer } from "msw/node"
import { mockApiHandlers } from "./mockApiHandlers.ts"

// mockApiHandlers를 이용해 MSW 서버 생성
const server = setupServer(...mockApiHandlers)
```

그리고 테스트 실행 전후로 서버를 관리하기 위한 코드를 작성해 준다.
```ts
// 모든 테스트가 시작하기 전에 MSW 서버를 실행
beforeAll(() => server.listen())

// 모든 테스트가 끝난 뒤에 MSW 서버를 종료
afterAll(() => server.close())
```
이렇게 세팅해두면 테스트 코드에서 MSW가 제공하는 가짜 API를 사용할 수 있다. API 호출이 필요한 컴포넌트를 테스트할 때 실제 서버 없이도 테스트가 가능해진다는 뜻이다.

<br />


## 결과

1. 프로젝트가 초기화되면 `Mocking enabled`가 나타난다.

![](https://velog.velcdn.com/images/khy2106/post/89de139b-16d4-4887-9124-4abf15837411/image.png)


2. 데이터가 정상적으로 불러와짐을 확인할 수 있다.

![](https://velog.velcdn.com/images/khy2106/post/9659e267-89f9-4662-a981-046aea6a357e/image.gif)



<br />


# 마무리하면서
내가 춘자라니처럼 **Next.js로 만든 미니 프로젝트에 굳이 API Mocking을 시도해야 할까?** 정답은 아닐 것이다. Next.js의 특성 때문에 MSW를 프로젝트에 적용하는 과정이 세팅부터 굉장히 까다로웠다. 

따라서 이런 미니 프로젝트는 단순히 Mock 데이터를 이용해 작업하는 게 훨씬 빠르고 효율적이었을 것이다. 특히 백엔드 API가 실제로 존재하지도 않는 상황에서 말이다.

하지만 실제 협업 상황에서는 다를 것이다. **프론트/백엔드 협업 환경에서는 병렬 개발이 필요한 순간이 반드시 온다.** 백엔드 API가 아직 준비되지 않은 상태에서도 프론트엔드 개발을 미리 진행해야 하는 상황 말이다.

또 테스트 환경을 구축할 때도 MSW가 빛을 발한다. 실제 API에 의존하지 않으면서도 안정적인 테스트를 작성할 수 있고, 에러 상황도 자유롭게 시뮬레이션해볼 수 있다.

이런 실무 환경을 대비해서 미리 연습해보는 건 무조건 도움이 된다고 본다. 이번에 테스트 코드 작성법도 배우고 Next.js에 대한 이해도 더 깊어졌으니, 프론트엔드 개발자로서 한걸음 더 성장한 것 같아 뿌듯하다...! 

참고로 Next.js에 MSW를 적용하는 과정에서 CJ 올리브영 테크 블로그의
[Next.js에서 MSW(Mock Service Worker)로 네트워크 Mocking하기
](https://oliveyoung.tech/2024-01-23/msw-frontend)글이 많은 도움이 됐다. 실제 Next.js에서 MSW를 도입하며 겪은 시행착오가 기록된 글이니 참고하면 좋을 것 같다.

MSW 세팅이 잘 되지 않아 힘든 일요일 밤이었다..ㅠㅠ 잘 쉬고 다음 과제 하러 가야지..!!
