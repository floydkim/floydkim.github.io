---


---

<h1 id="pwa알못들을-위한-progressive-web-app-설명서">PWA알못들을 위한 Progressive Web App 설명서</h1>
<p>원제: An explanation of Progressive Web Apps for the non-PWA crowd<br>
<a href="https://medium.freecodecamp.org/an-explanation-of-progressive-web-apps-for-the-non-pwa-crowd-8a400e275ea1">이 글</a>의 (의역을 포함한) 번역입니다.</p>
<hr>
<p>어플리케이션의 세계는 그리 오래되지 않은 과거에 두 카테고리로 구분되었습니다. 안드로이드와 iOS로 각각 어플리케이션을 만들게 되었죠. PWA. 길게 얘기하면 Progressive Web Applications 이라고 합니다. 아마 최근 몇 년 사이에 한번씩 들어보셨겠지만 PWA가 무엇인지는 잘 모르실겁니다. PWA의 인기가 높아지고 있으니 모든 소란이 무엇에 관한 것인지 알아보는 것이 좋을 겁니다.</p>
<p>이 글에서는, PWA가 무엇인지, 어떤 구성 요소로 이루어졌는지 살펴보고 여러분 스스로 하나 만들어 볼 수 있게 방법을 알려드리겠습니다.</p>
<h2 id="기본-the-basics">기본 (The Basics)</h2>
<p>PWA는 어플리케이션으로 변한 웹사이트입니다. 무슨 뜻이나면, Java나 Objective-C 코드(혹은 최신 모바일 코딩 언어들) 대신, 웹사이트를 만들듯이 어플리케이션 코드를 쓸 수 있다는 얘기입니다. HTML, CSS, (역자 주 : JavaScript같은)스크립트로 말이죠.</p>
<p>왜 여러분은 네이티브 어플리케이션 대신 PWA로 어플리케이션을 만들려 하시나요? PWA는 한 번 릴리즈 하고 나서 앱을 다시 퍼블리시 할 필요 없이 지속적으로 수정할 수 있습니다. 모든 코드가 서버에서 호스팅되고 APK나 IPA의 일부가 아니기 때문에 어떤 변경이든 실시간으로 일으킬 수 있습니다.</p>
<p>만약 네트워크 연결에 의존하는 어플리케이션을 사용해 본 적이 있으시다면, 네트워크 연결이 없을 때 아무것도 할 수 없게 되는 좌절감에 익숙하실 겁니다. PWA와 함께라면, 네트워크에 문제가 있어도 유저들에게 오프라인 경험을 제공해 줄 수 있습니다.</p>
<p>게다가 PWA는, 유저로 하여금 홈 스크린에 앱을 추가하도록 메시지를 띄울 수 있습니다. 네이티브 어플리케이션에는 없는 기능입니다.</p>
<h2 id="구성-요소-components">구성 요소 (Components)</h2>
<p>PWA 앱을 퍼블리시 하고 싶다면, PWA에 관련한 표준을 잘 지키셔야 합니다. 각각의 PWA는 다음 구성요소들로부터 만들어집니다 :</p>
<ul>
<li>웹 앱 매니페스트 (manifest)</li>
<li>서비스 워커 (service worker)</li>
<li>설치 경험 (install experience)</li>
<li>HTTPS</li>
<li>APK 생성</li>
<li>Lighthouse 검사 (audit)</li>
</ul>
<h2 id="매니페스트-the-manifest">매니페스트 (The Manifest)</h2>
<p>json 형식의 설정 파일입니다. 어떻게 유저 화면에 표현될지를 포함해 PWA 앱의 다양한 세팅을 변경하도록 해줍니다. 아래는 예시입니다. :</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token comment">// manifest.json</span>
<span class="token punctuation">{</span>
  <span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"My PWA"</span><span class="token punctuation">,</span>
  <span class="token string">"short_name"</span><span class="token punctuation">:</span> <span class="token string">"PWA"</span><span class="token punctuation">,</span>
  <span class="token string">"icons"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">{</span>
    <span class="token string">"src"</span><span class="token punctuation">:</span> <span class="token string">"your_icon.png"</span><span class="token punctuation">,</span>
      <span class="token string">"sizes"</span><span class="token punctuation">:</span> <span class="token string">"128x128"</span><span class="token punctuation">,</span>
      <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"image/png"</span>
    <span class="token punctuation">}</span><span class="token punctuation">]</span><span class="token punctuation">,</span>
  <span class="token string">"start_url"</span><span class="token punctuation">:</span> <span class="token string">"/index.html"</span><span class="token punctuation">,</span>
  <span class="token string">"display"</span><span class="token punctuation">:</span> <span class="token string">"standalone"</span><span class="token punctuation">,</span>
  <span class="token string">"background_color"</span><span class="token punctuation">:</span> <span class="token string">"#3E4EB8"</span><span class="token punctuation">,</span>
  <span class="token string">"theme_color"</span><span class="token punctuation">:</span> <span class="token string">"#2F3BA2"</span>
<span class="token punctuation">}</span>
</code></pre>
<p>반드시 name이나 short name key를 입력해야 합니다. 둘 다 설정하면 short name이 홈스크린과 런쳐에 사용됩니다. name 값은 ‘홈 스크린에 추가하기’ 경험(혹은 어플리케이션 설치 프롬프트)에 사용됩니다.</p>
<p>display는 네가지 다른 값을 가집니다:</p>
<ul>
<li>fullscreen : 앱이 열릴 때 전체 화면을 차지하도록 허용합니다</li>
<li>standalone : 앱이 네이티브 어플리케이션처럼 보이게 합니다. 브라우저의 요소들을 감춥니다.</li>
<li>minimal-ui : 약간의 브라우저 컨트롤을 제공합니다. (크롬 모바일에서만 지원합니다)</li>
<li>browser : 이름이 말해주듯이 앱이 브라우저 환경의 경험과 동일하게 표현됩니다.</li>
</ul>
<p>또한 orientation(가로방향/세로방향)을 설정할 수 있고, 앱 안에 있는것으로 간주되는 페이지들의 scope를 설정할 수 있습니다.<br>
(역자 주 : 찾아보니 scope를 벗어나는 페이지 요청의 경우 앱의 유효범위 밖으로 이동하는 것으로 간주하고 새로운 브라우저를 실행하거나, deep link 기능을 통해 다른 네이티브 앱으로 전환시킬 수 있다고 합니다. 어렵네요. <a href="https://w3c.github.io/manifest/">w3c 스펙</a>)</p>
<p>잊지 말고 메인 HTML 파일에 매니페스트를 추가하세요. 다음 메타 태그를 head 태그 안에 넣으세요.</p>
<pre class=" language-html"><code class="prism  language-html"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>link</span> <span class="token attr-name">rel</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>manifest<span class="token punctuation">"</span></span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/manifest.json<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
</code></pre>
<h2 id="서비스-워커-the-service-worker">서비스 워커 (The Service Worker)</h2>
<p>서비스 워커는 브라우저상에서 웹사이트 백그라운드 영역에 실행되는 구성 요소입니다. 푸쉬 알림을 포함해 아주 다양한 기능을 포함하고 있습니다. 자산(asset)을 캐싱해뒀다가 오프라인 경험을 위해 제공하기도 하며, 인터넷 연결이 불안정 할 때는 안정한 상태가 될 때 까지 처리를 연기하는 기능도 가지고 있습니다. 서비스 워커의 모든 내용을 다루기엔 양이 너무 방대해서 <a href="https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API">깊게</a> 다루지 않겠습니다. 하지만 여러분의 PWA에 사용할 수 있는 바닐라 예제를 제공할 것입니다.</p>
<p>서비스 워커에 관련된 코드를 sw.js 라는 파일에 저장하는 것이 보편적입니다. (역자 주: 당장 크롬 콘솔을 열어봐도 정말 sw.js로 서비스 워커를 등록한 사이트가 있더라구요. 근데 흔치는 않은 것 같습니다.)</p>
<p>🖐서비스 워커는 같은 디렉토리나 하위 디렉토리의 파일에만 액세스 할 수 있기 때문에 서비스 워커의 위치는 중요합니다!<br>
(역자 주 : 상위 디렉토리의 파일엔 접근할 수 없으니 서비스워커 자체의 위치, 관련 파일의 위치에 신경쓰라는 얘기로 이해됩니다.)</p>
<p>서비스 워커는 다음 페이즈들로 요약할 수 있습니다 :</p>
<ul>
<li>등록 (Registration)</li>
<li>설치/활성화 (Installation/Activation)</li>
<li>다양한 이벤트에 반응 (Responding to various events)</li>
</ul>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">//메인 html 파일(index.html) 안의 body 태그 끝의 script 태그 안에 아래 코드를 붙여 넣으세요.</span>
<span class="token comment">//서비스 워커 등록</span>
<span class="token keyword">if</span><span class="token punctuation">(</span><span class="token string">'serviceWorker'</span> <span class="token keyword">in</span> navigator<span class="token punctuation">)</span> <span class="token punctuation">{</span>
  navigator<span class="token punctuation">.</span>serviceWorker
           <span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span><span class="token string">'/sw.js'</span><span class="token punctuation">)</span>
           <span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Service Worker Registered"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span><span class="token operator">--</span>

<span class="token comment">//sw.js 파일 안</span>
<span class="token comment">//이 곳에서 polyfill을 구할 수 있습니다 : https://github.com/dominiccooney/cache-polyfill/blob/master/index.js</span>
<span class="token function">importScripts</span><span class="token punctuation">(</span><span class="token string">'/serviceworker-cache-polyfill.js'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">//'install' 이벤트를 리스닝하며, 사이트 자산(assets)을 캐싱</span>
self<span class="token punctuation">.</span><span class="token function">addEventListener</span><span class="token punctuation">(</span><span class="token string">'install'</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
 e<span class="token punctuation">.</span><span class="token function">waitUntil</span><span class="token punctuation">(</span>
   caches<span class="token punctuation">.</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token string">'your_app_name'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>cache<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token keyword">return</span> cache<span class="token punctuation">.</span><span class="token function">addAll</span><span class="token punctuation">(</span><span class="token punctuation">[</span>
       <span class="token string">'/'</span><span class="token punctuation">,</span>
       <span class="token string">'/index.html'</span><span class="token punctuation">,</span>
        <span class="token comment">// 필요한 자산(assets)들을 여기에 넣으세요</span>
     <span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   <span class="token punctuation">}</span><span class="token punctuation">)</span>
 <span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">//'fetch' 요청을 리스닝하며, 캐시에서 발견될 경우 캐시로부터 파일을 가져옴</span>
self<span class="token punctuation">.</span><span class="token function">addEventListener</span><span class="token punctuation">(</span><span class="token string">'fetch'</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
 event<span class="token punctuation">.</span><span class="token function">respondWith</span><span class="token punctuation">(</span>
   caches<span class="token punctuation">.</span><span class="token function">match</span><span class="token punctuation">(</span>event<span class="token punctuation">.</span>request<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>response<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token keyword">return</span> response <span class="token operator">||</span> <span class="token function">fetch</span><span class="token punctuation">(</span>event<span class="token punctuation">.</span>request<span class="token punctuation">)</span><span class="token punctuation">;</span>
   <span class="token punctuation">}</span><span class="token punctuation">)</span>
 <span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

</code></pre>
<h2 id="설치-경험-install-experience">설치 경험 (Install Experience)</h2>
<p>PWA의 특별한 기능 중 하나는 설치 경험입니다. 유저가 여러분의 어플리케이션을 설치하도록 알려주는 것을 일컫습니다. 유저에게 이 기능을 선보이도록 하기 위해서는 <em>beforeinstallprompt</em>라는 이벤트를 리스닝 해야 합니다. 아래 코드는 샘플인데, 어플리케이션을 추가할지 유저의 선택에 따라 로직을 활성화하는 플로우 입니다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">var</span> savedPrompt <span class="token operator">=</span> <span class="token keyword">null</span><span class="token punctuation">;</span>

window<span class="token punctuation">.</span><span class="token function">addEventListener</span><span class="token punctuation">(</span><span class="token string">'beforeinstallprompt'</span><span class="token punctuation">,</span> beforeInstallPrompt<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">function</span> <span class="token function">beforeInstallPrompt</span><span class="token punctuation">(</span>event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
   event<span class="token punctuation">.</span><span class="token function">preventDefault</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   savedPrompt <span class="token operator">=</span> event<span class="token punctuation">;</span>
   <span class="token comment">//이곳에 여러분의 어플리케이션을 홈 스크린에 추가하기 위한 UI를 보여주는 로직을 구현하세요 (아마도 버튼이겠죠)</span>
<span class="token punctuation">}</span>

<span class="token comment">//유저가 여러분의 UI에서 버튼을 클릭했을 때 이 메서드를 호출하세요</span>
<span class="token keyword">function</span> <span class="token function">userClickedAddToHome</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
   savedPrompt<span class="token punctuation">.</span><span class="token function">prompt</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   
   savedPrompt<span class="token punctuation">.</span>userChoice
    <span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>choiceResult<span class="token punctuation">)</span><span class="token punctuation">{</span>
  <span class="token keyword">if</span> <span class="token punctuation">(</span>choiceResult<span class="token punctuation">.</span>outcome <span class="token operator">===</span> <span class="token string">'accepted'</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">//유저가 홈 스크린에 어플리케이션 추가에 동의</span>
  <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
    <span class="token comment">//유저가 홈 스크린에 어플리케이션 추가를 거부</span>
  <span class="token punctuation">}</span>
  savedPrompt <span class="token operator">=</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="https">HTTPS</h2>
<p>그리 오래되지 않은 과거에, 웹사이트들은 너무나도 흔한 http 프로토콜을 여전히 사용할 수 있었습니다. 최근 보안 영역과 크롬에 생긴 변화로 인해, https 프로토콜로 동작하지 않는 웹사이트들은 안전하지 않은 것으로 표시됩니다. 심지어 여러분의 웹사이트가 유저 데이터나 민감한 커뮤니케이션 정보를 다루지 않더라도 https로 전환하는 것은 좋은 실천사항입니다.</p>
<p>그리고 제가 앞서 말씀드렸듯이, PWA를 릴리즈 하길 원한다면 https 프로토콜을 사용해야만 합니다. 만약 도메인을 구하고, 적절한 호스팅 서비스를 찾고, SSL을 적용하는 소동에 말리고 싶지 않다면, Github라는 쉬운 옵션을 선택할 수 있습니다. 계정이 있다면, 레포를 열고 GitHub Page를 세팅할 수 있습니다. 이 과정은 아주 간편하고 직관적이며 Github의 일부로서 HTTPS를 사용하는 보너스도 있습니다.</p>
<h2 id="apk-생성-creating-an-apk">APK 생성 (Creating An APK)</h2>
<p>구글 플레이스토어에 PWA를 배포하기 위해서 우리는 APK르르 생성해야 합니다. <a href="https://pwa2apk.com/?ref=steemhunt">PWA2APK</a>라는 인기있는 툴을 사용해 어려운 작업을 대신하게 할 수 있습니다. 하지만 만약 직접 하는 방법을 배우고 싶으시다면, 계속 읽어주세요.</p>
<p>구글은 PWA를 플레이스토어에 통합하는 새로운 방법을 소개했는데, Trusted Web Activity, TWA라고 부릅니다. 몇 개의 간단한 단계를 거쳐 TWA를 만들어 플레이스토어에 업로드 하는 방법을 배우게 되실겁니다.</p>
<ol>
<li>안드로이드 스튜디오를 열고 빈 activity를 만드세요</li>
<li>프로젝트의 build.gradle 파일로 가서 jitpick repository를 추가하세요</li>
</ol>
<pre><code>allprojects {
   repositories {
       google()
       jcenter()
       maven { url "https://jitpack.io" } // &lt;---
   }
}
</code></pre>
<ol start="3">
<li>모듈 레벨 build.gradle 파일로 가서 아래 코드를 추가해 Java8 호환성을 활성화 하세요</li>
</ol>
<pre><code>android {
    compileSdkVersion 28
    defaultConfig {
        applicationId "your_application_id"
        minSdkVersion 16
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
      
    //Add the lines below
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
</code></pre>
<ol start="4">
<li>TWA 지원 라이브러리를 디펜던시로 추가하세요</li>
</ol>
<pre><code>dependencies {
   implementation 'com.github.GoogleChrome.custom-tabs-client:customtabs:d08e93fce3'
}
</code></pre>
<ol start="5">
<li>activity XML을 application 태그들 사이의 AndroidManifest 파일 안에 추가하세요</li>
</ol>
<pre><code>&lt;activity
            android:name="android.support.customtabs.trusted.LauncherActivity"&gt;
           &lt;meta-data
               android:name="android.support.customtabs.trusted.DEFAULT_URL"
               android:value="enter the url of your PWA" /&gt;
           &lt;intent-filter&gt;
               &lt;action android:name="android.intent.action.MAIN" /&gt;
               &lt;category android:name="android.intent.category.LAUNCHER" /&gt;
           &lt;/intent-filter&gt;

           &lt;intent-filter&gt;
               &lt;action android:name="android.intent.action.VIEW"/&gt;
               &lt;category android:name="android.intent.category.DEFAULT" /&gt;
               &lt;category android:name="android.intent.category.BROWSABLE"/&gt;
               
               &lt;data
                 android:scheme="https"
                 android:host="enter your PWA url"/&gt;
           &lt;/intent-filter&gt;
        &lt;/activity&gt;
</code></pre>
<p>android:value와 android:host를 여러분의 URL로 바꾸세요</p>
<ol start="6">
<li>디지털 assets 링크를 이용해 어플리케이션과 웹사이트 사이의 관계를 생성해야 합니다. 다음 코드를 strings.xml 파일 안에 붙여넣으세요.</li>
</ol>
<pre><code>&lt;resources&gt;

&lt;string name="asset_statements"&gt;
        [{
            \"relation\": [\"delegate_permission/common.handle_all_urls\"],
            \"target\": {
                \"namespace\": \"web\",
                \"site\": \"https://your PWA url\"}
        }]
    &lt;/string&gt;
    
&lt;/resources&gt;
</code></pre>
<p>site 값을 여러분의 URL을 가리키도록 변경하세요</p>
<ol start="7">
<li>AndroidManifest.xml 파일 안에 application 태그의 child로 다음 메타 태그를 추가하세요</li>
</ol>
<pre><code>&lt;application&gt;
  &lt;meta-data
            android:name="asset_statements"
            android:resource="@string/asset_statements" /&gt;
  &lt;activity&gt;...&lt;/activity&gt;
&lt;/application&gt;
</code></pre>
<ol start="8">
<li>
<p><a href="https://developer.android.com/studio/publish/app-signing#generate-key">업로드 키와 keystore를 생성하세요</a></p>
</li>
<li>
<p>다음 명령어를 이용해 SHA-256 키를 만드세요</p>
</li>
</ol>
<pre><code>keytool -list -v -keystore keystore_path -alias keystore_alias -storepass keystore_password -keypass key_password
</code></pre>
<p>keystore와 업로드 키를 생성할 때 입력한 내용들을 기억하세요</p>
<ol start="10">
<li>
<p><a href="https://developers.google.com/digital-asset-links/tools/generator">assets 링크 생성기</a>로 가서, SHA-256 핑거프린트, 여러분의 어플리케이션 패키지, 웹사이트의 도메인을 공급하세요.</p>
</li>
<li>
<p>여러분의 웹사이트의 디렉토리 중 /.well-known 이라는 위치 아래의 assetlinks.json파일 안에 결과물을 넣으세요. 크롬은 이 목적지(destination)을 확실하게 탐색 할 것입니다.</p>
</li>
<li>
<p><a href="https://medium.freecodecamp.org/how-to-publish-an-application-in-the-play-store-8ddcc6dc3587">signed APK를 생성하고 플레이스토어에 업로드하세요</a></p>
</li>
</ol>
<h2 id="lighthouse">Lighthouse</h2>
<p>저는 여러분이 PWA를 퍼블리시 하기 위해 어떤 것들이 요구되는지에 대한 감을 잃으셨으리라 확신합니다. 고려해야 할 사항이 너무 많아서 요구 사항들이 무엇인지 감을 잃기 쉽습니다.</p>
<p>운좋게도, 구글은 <a href="https://developers.google.com/web/tools/lighthouse/#devtools">Lighthouse</a>를 만들었습니다. 크롬 개발자 도구(크롬 버전 60부터)에서 찾을 수 있습니다. 브라우저에서 우클릭 후에 검사(inspect)를 클릭하는 것 만으로도 접근할 수 있습니다. 패널이 열리면 오른쪽 끝에 Audits 탭을 볼 수 있습니다.</p>
<p>Run audits 버튼을 클릭해 감사(audit)을 수행할 수 있습니다. 1~2분 정도 걸리지만 결과물로 유익하고 그래픽적으로 표현된 PWA 랭크를 세가지 속성의 관점으로 받아볼 수 있습니다.</p>
<ul>
<li>성능 (Performance)</li>
<li>접근성 (Accessibility)</li>
<li>Best Practices</li>
</ul>
<p>각 속성별로 여러분의 어플리케이션이 요구사항을 어디서 통과했고 어디서 통과하지 못했는지 알 수 있습니다. 이걸 통해 여러분은 어딜 고쳐야 하고 어느 부분이 기준을 충족하는지 볼 수 있게 됩니다. 관심이 있으시면, <a href="https://developers.google.com/web/progressive-web-apps/checklist#baseline">여기서</a> 체크리스트를 볼 수 있습니다.</p>
<h2 id="pwa-it-up">PWA it up</h2>
<p>우리는 여정의 끝에 도착했고 여러분들이 PWA의 세계를 항해하기 위한 준비가 더 잘 되었다고 느끼길 바랍니다. 이 글은 최근 제가 PWA를 만들면서 겪은 과정으로부터 영감을 받아 작성되었습니다. 아래 링크를 통해 확인해 보세요. :<br>
<a href="https://play.google.com/store/apps/details?id=com.tomerpacific.androidmenugenerator">Android Menu XML Generator - Apps on Google Play</a></p>
<hr>
<p><em><strong>역자 주</strong></em><br>
<a href="https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps">MDN</a>에도 상세하고 긴 글이 있습니다.</p>

