---


---

<h1 id="expo와-이별하기">EXPO와 이별하기</h1>
<p>React-Native를 이용해 모바일 앱을 만들 때, EXPO는 개발자가 앱 코딩을 빨리 시작할 수 있도록 도와줍니다.<br>
Xcode나 Android 프로젝트를 만들고, 프로젝트를 세팅하는 번거로움 없이 바로 코딩을 시작할 수 있게 해주는 유용한 툴입니다.</p>
<p>그런데 EXPO는 네이티브 언어로 된 라이브러리를 이용할 수 없다는 한계가 있습니다. 그래서 EXPO를 이용해 앱 개발을 하다 보면 <code>$ expo eject</code> 명령을 통해 EXPO와 이별할 수 밖에 없게 됩니다.</p>
<p>이 글에서는 eject를 하는 과정이나 시행 착오를 다루지 않습니다.<br>
대신 React-Native는 할수있는 네이티브 모듈 사용이 EXPO에서는 왜 안되는건지 고민해보겠습니다.</p>
<h2 id="expo의-동작-환경">EXPO의 동작 환경</h2>
<p><img src="https://lh3.googleusercontent.com/uI0fYPxqo0urSM60u_FbYdGwJmSspF5odKhn-RQAQufCtbJG5j9aFxuPqJ_6SXcFgCfBl2IfWVw" alt="expo 동작 환경"></p>
<p>우선 어떤 구조로 EXPO가 동작하는지 살펴보겠습니다.</p>
<p>EXPO는 로컬 개발환경에 두개의 서버를 띄워 동작합니다.<br>
EXPO 모바일 클라이언트에 react-native가 번들링 한 JavaScript 파일을 내려주기 위한 서버(초록색),<br>
EXPO-CLI와 expo 모바일 클라이언트 둘 사이의 통신을 위한 서버(핑크색)을 하나 띄웁니다.</p>
<p>이 중 핵심은 엑스포 클라이언트입니다.<br>
작성한 코드가 모바일 네이티브 환경에서 직접 구동되는것이 아니고, 받아온 앱을 엑스포 클라이언트 안에서 실행시킵니다.</p>
<p>EXPO 공식문서를 통해 위 사실을 알 수 있었습니다.</p>
<blockquote>
<p>" EXPO 안에 들어가는 앱은 순수 JavaScript로 작성되고, 절대로 네이티브 iOS나 Android 레이어까지 내려가지 않는다. 이것은 EXPO의 핵심 철학이고, EXPO를 빠르게, 사용하기 좋게 만들어주는 부분이다. "</p>
</blockquote>
<h2 id="expo-앱과-native-앱의-차이">EXPO 앱과 Native 앱의 차이</h2>
<p><img src="https://lh3.googleusercontent.com/cYu8NWNwEl8EaW7nqJZ342bG0o36GSdCgEqCkE_pHhB4llyDnXgKy_Tf_Gtp8lSEXr2BCYELkSw" alt="EXPO와 Native App 비교"></p>
<h5 id="꾹꾹이는-제가-팀-프로젝트로-만들었던-앱의-이름입니다.">(<code>꾹꾹이</code>는 제가 팀 프로젝트로 만들었던 앱의 이름입니다.)</h5>
<p>먼저 오른쪽에 있는 네이티브 앱인 카카오톡을 볼까요?<br>
카카오톡같은 네이티브 앱은 코드를 컴파일하고, 모바일 기기에 설치되어 OS 레이어와 직접 상호작용하며 네이티브 환경에서 동작합니다.</p>
<p>반면에 우리가 작성한 자바스크립트 코드는 EXPO 모바일 클라이언트 안에서만 동작하고, JavaScript 엔진으로만 동작하기 때문에 네이티브 언어 라이브러리를 소화해 낼 수 없습니다.<br>
다만 EXPO 앱은 네이티브 앱이라서 OS 레이어와 상호작용을 대신 합니다. 그리고 코드를 작성할 때, React 컴포넌트로 만들어놓은 모듈들을 이용해서 단순한 웹뷰 수준을 벗어나서 네이티브한 기능을 구현할 수 있습니다.</p>
<p>그러나 그 기능이 EXPO에서 제공하는 기능들로 제한되기 때문에, EXPO에서 지원하지 않는 Bluetooth 컨트롤이나, 네이티브 언어로 된 모듈을 이용하려 할 때 <strong>eject</strong> 명령을 사용하게 됩니다.</p>
<h2 id="expo-eject">$ expo eject</h2>
<p><img src="https://lh3.googleusercontent.com/nuTs6LdeiUJkrerF4jm78VzOreZ4BWnm6KRh8WM8n-k4-mF_3TOsV5ffbOQnK-TFhl43tC-hXCA" alt="expo eject"></p>
<p>eject를 하면 순수 JavaScript로 작성된 앱을 엑스포 모바일 클라이언트에서<br>
꺼내줍니다. 다시 말해, EXPO 클라이언트 없이 네이티브 환경에서 우리 앱이 동작하게 됩니다.</p>
<p>공식문서에도 관련 내용이 있습니다.</p>
<blockquote>
<p>" <em>RN Core나 Expo SDK에서 지원하지 않는</em> 네이티브 모듈들을 사용해야 할 때 eject를 한다.<br>
eject하면 순수 JavaScript로 이루어진 프로젝트를 Expo 모바일 클라이언트로부터 꺼내준다.<br>
그리고 Xcode나 Android Studio 네이티브 프로젝트를 만든다.<br>
eject 후에 여전히 Expo SDK에 dependency가 있긴 하지만, 프로젝트는 더이상 엑스포 클라이언트 안에 살지(live) 않는다. "</p>
</blockquote>
<p>eject를 할 때, ExpoKit이라는 네이티브 라이브러리를 남겨놓으면 EXPO에 내장된 다양한 React 컴포넌트들을 이용해 네이티브 기능들을 쉽게 사용할 수 있습니다.<br>
실제로 꾹꾹이 프로젝트에서도, EXPO의 Google 지도 컴포넌트(<code>&lt;MapView /&gt;</code>)를 이용해 빠르게 지도 기능을 구현할 수 있었습니다.</p>
<p>한편 eject를 다시 돌이키는것도 가능합니다. 공식문서에 방법이 소개되어 있지만 eject로 인해 소실된 일부 정보를 manual하게 다시 잡아주는 과정이 필요합니다.</p>
<h2 id="workflow-with-expo">workflow with EXPO</h2>
<p><img src="https://lh3.googleusercontent.com/w7Ggise6EWgPjaUN5GPOOuagjCjifWTxTOu5bNGnu1XFkT4Nx8CLj9DG-apk2s9JdefLV8ozOco" alt="workflow with expo"></p>
<p>엑스포의 장점은 결국, 개발자의 생산성을 높이는데 있다고 생각합니다.</p>
<p>초기엔 EXPO를 사용해서 React-Native 앱 개발의 생산성을 높이고, native한 기능 구현이 남은 시점에 eject해서 목표하는 기능을 구현하는 것이 효율적인 프로세스가 아닐까 생각됩니다.</p>
<p>EXPO에 대한 모든 것은 <a href="https://docs.expo.io/versions/latest/">이 곳</a>(공식문서)에서 보실 수 있습니다.</p>

