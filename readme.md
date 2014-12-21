# Polymer: Create a reusable Web Components with Daum Map

### Steps

## 0. Prerequisites

이 Codelab 은 Polymer 에 대한 기초적인 지식이 있는 개발자를 대상으로 하고 있습니다. CLI 환경에서 작업이 익숙하거나 Node.js 로 만들어진 도구 사용에 무리가 없으셔야 합니다. 실제 Polymer APIs 와 attributes 등에 대한 자세한 설명이 없습니다. 충분한 설명을 위해서는 [Polymer Polytechnic 의 Codelabs](http://goo.gl/1WeU2v) 과정을 참고하세요.

Codelab 에서는 Yeoman 을 사용하여 Seed element 와 Github page 를 생성하고 있습니다. 따라서 아래 도구들의 설치가 미리 설치되어 있어야 합니다. 자료가 작성된 환경은 Mac OSX 입니다.

- git 과 github 계정 사용 가능
- 다음맵 API 를 받을 수 있는 다음 계
- Yeoman, Bower, Grunt 설치
- Polymer 를 위한 공식 Generator 인 generator-polymer 설치


		# Note: Yeoman, generator-polymer 설치
		npm install -g yo bower generator-polymer

## 1. Getting set up

### Create a new project directory

`yo` 명령을 사용하여 새로운 Seed element 프로젝트를 생성하겠습니다. 주의하실것은 생성된 프로젝트는 Polymer element 패키지와 동일한 레벨의 경로를 가지게 됩니다. 따라서 의존성 있는 패키지를 설치할 경우 프로젝트 경로의 부모 경로에 설치하게 됩니다. 따라서 프로젝트는 디렉토리 구조는 다음과 같이 구성하는 것이 일반적입니다.

```
\project root
	\components: 개발할 Component 와 의존성 있는 Component 가 설치될 경로
		\core-icon
		\paper-input
		\YOUR-PROJECT: 실제 프로젝트 경로
			\bower.json
			\seed-element.html
			\demo.html
```

따라서 아래 명령으로 프로젝트 최상위 경로와 Components 를 위한 경로를 생성합니다.

	mkdir -p daum-map/components && cd $_

### Generate a new Polymer seed element via yeoman

이제 `daum-map/components` (경로)에서 Yeoman 의 yo 명령을 통해서 새로운 Seed element 를 생성해보겠습니다.

	yo polymer:seed daum-map

명령을 실행하면 간단한 질문 (github id) 에 대답을 하면 Yeoman 에서는 프로젝트에 필요한 의존성이 있는 파일을 설치하게 됩니다. 의존성 있는 파일의 목록은 node 패키지는 `package.json` 그리고 bower 패키지는 `bower.json` 에 목록이 있습니다. 모두 설치가 끝나면 `ls` 명령으로 아래와 같이 구성이 되었는지 확인합니다.

```
core-component-page
daum-map
polymer
web-component-tester
webcomponentsjs
```

daum-map 이라는 새로운 Seed element 가 daum-map/components/ 아래에 생성되었으며 Polymer 프로젝트에 필요한 라이브러리들이 설치되었습니다.

## 2. Preview the element

선호하시는 간단한 웹서버를 구동해서 생성된 component 를 위한 페이지를 확인합니다. 
추가로 생된된 demo.html, index.html 으로 컴포넌트의 정보를 확인할 수 있습니다.index.html 은 [core-component-page](https://github.com/Polymer/core-component-page) 에서 [JSDoc](http://en.wikipedia.org/wiki/JSDoc) 스타일로 작성된 주석을 읽어 들여서 문서를 문서를 만들어 줍니다. 실제로 외부에 노출되는 인터페이스의 경우 주석을 추가해서 외부의 개발자가 사용하기 쉽도록 해주어야 합니다. Yeoman 에서 생성된 seed element 에는 JSDoc 의 boilerplate 주석이 포함되어 있습니다.

```
# node
http-server

# python 
```

## 2. Create a Daum Map

### Cleanup Boilerplates

먼저 자동으로 생성된 코드를 먼저 정리하겠습니다. `<template>` 내의 아래 코드를 삭제하세요.

```
# daum-map/components/daum-map/daum-map.html:L21:22

<h1>Hello from daum-map</h1>
<content></content>
```

그 다음 Polymer({}) 에서 자동으로 생성된 아래 properties 와 methods/events 삭제하세요. 일부 주석과 구현은 축약되었습니다. 주의하세요

```
# daum-map/components/daum-map/daum-map.html:L29:82

/**
  ...
 */
author: 'Dimitri Glazkov',

/**
  ...
 */
fancy: false,

ready: function() {
  ...
},

/**
  ...
 */
sayHello: function(greeting) {
  ...
},

/**
  ...
*/
fireLasers: function() {
  ...
}
```

이제 다음맵을 위한 코드를 추가할 준비가 되었습니다.

### Get a Daum API key for Daum Map

다음 지도를 사용하기 위해서는 [지도형 API 신청](http://goo.gl/Rs0yco) 페이지에서 API 를 키를 먼저 발급 받으셔야 합니다.

키를 등록할때는 HTTP Referrer 를 입력받는데 로컬에서 개발할 경우에는 http://localhost:[PORT] 를 이후에는 http://[GITHUB-ID].github.io 를 사용하셔야 합니다. 현재는 수정이 안되니 두개를 발급하신다고 생각하지면 됩니다.

등록이 성공적으로 끝나게 되면 아래와 같은 키가 발급됩니다.

	a043a4e407075e23d378123cc6bdeef2373586867914e4

### Create a Daum Map with Options

이제 발급받은 키를 사용해서 다음에서 맵용 라이브러리를 로딩하고 보여지도록 해보겠습니다.

1. 먼저 비어있는 Polymer({}) 에 생성될 맵 object 를 저장할 property 를 추가합니다.

    ```
    Polymer({
      map: undefined
    });
    ```

1. `daum-map` element 에 기존에 있던 attributes 는 제거하고 `apikey` 를 추가합니다.
		
    ```
    <polymer-element name="daum-map" attributes="notitle author">
    
    >>
    
    <polymer-element name="daum-map" attributes="apikey">
    ```

1. apikey 를 저정하기 위해서 Polymer:apikey property 를 추가합니다.

    ```
    Polymer({
      ...
      apikey: ''
    });
    ```

1. 생성된 다음맵에서 사용할 container element 를 추가합니다. 기존 `<template>` 안의 html 코드를 삭제합니다.

    ```
    <template>

    <link rel="stylesheet" href="daum-map.css" />

      <h1>Hello from daum-map</h1>
      <content></content>

    </template>

    >>

    <template>

    <link rel="stylesheet" href="daum-map.css" />

      <div id="map"></div>

    </template>
    ```

1. 맵에서 사용될 기본정보 위도/경도/레벨을 저장할 property 를 추가합니다. 각각 latitude / longitude / level 로 추가하고 제주 다음지사의 기본 위도/경도를 기본값으로 추가합니다. 

    ```
    Polymer({
      ...
      latitude: 33.450701,
      longitude: 126.570667,
      level: 3
    });
    ```

1. 레벨의 경우 attribute 로 값을 받을 수 있도록 attributes 에도 추가합니다.
    
    ```
    <polymer-element name="daum-map" attributes="apikey">
    
    >>

    <polymer-element name="daum-map" attributes="apikey level">
    ```

1. 실제 맵컨테이너의 크기를 결정할 width/height 를 추가합니다. 이 property 는 외부에서 선언시에 attribute 에서도 받을 수 있도록 attributes 에도 추가합니다.

    ```
    <polymer-element name="daum-map" attributes="apikey level">
    
    >>

    <polymer-element name="daum-map" attributes="apikey level width height">
    ```

1. width / height 를 저장하고 변경사항을 추적하기 위해서 property 로 추가합니다.
    
    ```
    Polymer({
      ...
      width: 0,
      height: 0
    });
    ```

    - 0 으로 초기화를 하면 이후 넘겨진 값은 숫자가 포함된 경우 Number 타입으로 전달됩니다

1. [Polymer:ready](http://goo.gl/uzxapk) method 를 추가하고 내부에 다음맵 라이브러리를 로딩하는 코드를 추가합니다. 관련해서 간단한 유틸 method, 'appendScript' 도 추가합니다. 
    
    ```
    Polymer({
      ...
      
      appendScript: function(src, onload) {
        var script = document.createElement('script');
        script.src = src;
        script.onload = onload.bind(this);
        document.head.appendChild(script);
      },

      ready: function() {
        var _this = this;

        _this.$.map.style.width = _this.width + 'px';
        _this.$.map.style.height = _this.height + 'px';

        this.appendScript('//apis.daum.net/maps/maps3.js?autoload=false&apikey=' + this.apikey, function() {
          daum.maps.load(function() {
            _this.map = new daum.maps.Map(_this.$.map, {
              center: new daum.maps.LatLng(_this.latitude, _this.longitude),
              level: 3
            });
          });
        });
      }
    });
    ```

    - 라이브러리 로딩시에 `autoload` 를 사용하는 것을 유의하세요.
    - 다음맵 API 에 대한 더 자세한 사항은 [Daum 지도 Web API Documentation](http://goo.gl/4EVJ8E) 를 참고하세요)

## 3. Declare `<daum-map>` on Your `demo.html`

구현된 `daum-map` component 가 동작하는지 확인 해보겠습니다. `demo.html` 에 기존에 생성된 코드를 삭제하고 `<daum-map>` 을 선언해서 사용해보겠습니다. 선언시에 `<daum-map>` 에서 노출한 `apikey, width, height` attributes 에 값을 넘기도록 하겠습니다.

```
# daum-map/components/demo.html:L11

<body unresolved>

  <p>An `daum-map` looks like this:</p>

</body>

>>

<body unresolved>

  <daum-map apikey="[YOUR-KEY]" width="400px" height="400px" level="10"></daum-map>

</body>
```

선언후에 다음맵이 제대로 보이는지 확인해보겠습니다.

## 4. Controlling Daum Map

이제 외부에서 `<daum-map>` component 로 데이터를 셋팅하면 넘겨진 값으로 다음맵을 조작하는 코드를 구현해보도록 하겠습니다. 여기에서는 Polymer 의 observing properties 방법중에서 가장 쉽게 property 변경값을 처리할 수 있는 [Changed Watcher](http://goo.gl/YGwbSv) 를 사용하겠습니다. 따라서 `propertyNameChanged` 형태로 method 를 선언하고 각 method 를 구현하도록 하겠습니다.

### Change LatLng

맵에서 가장 기본적인 동작인 위도/경도 값을 이용해서 맵의 중앙값을 변경해서 보여지는 맵의 위치를 변경하도록 해보겠습니다. 먼저 `demo.html` 에 값을 조작할 수 있는 컨트롤을 배치하겠습니다. 컨트로은 `paper-slider` 를 사용하고 각 컨트롤을 구분하기 위해서 `core-label` 를 사용하도록 하겠습니다.

1. 이후 좀 더 보기 쉬운 화면을 위해서 `demo.html` 의 레이아웃을 번경하겠습니다. 제목과 좌측에는 컨트롤 패널을 우측에는 다음맵이 보여지도록 조정하였습니다.

    ```
    <h2>Daum Map based on Web Components</h2>
    <div horizontal layout>
      <div vertical layout style="width:406px;margin-right:26px">
      </div>
    </div>
    <div flex>
        <daum-map apikey="a043a4e407075ed3781cc6bdeef73586867914e4" width="400px" height="400px" level="10"></daum-map>
    </div>
    ```

1. 좌측 컨트롤 영역에 LatLng 를 변경하기 위한 컨트롤을 추가하려고 합니다. 먼저 `paper-slider` 를 사용하기 위해서 bower 명령으로 해당 element 를 추가합니다. **명령은 반드시 프로젝트 경로인 `daum-map/components/daum-map` 에서 실행되어야 합니다.**  

    ```
    bower install --save Polymer/paper-slider
    ```

1. 추가된 `paper-slider` 를 import 하는 `<link>` element 를 `demo.html` 의 `<head>` 안에 추가합니다.

    ```
    <head>
      <meta>
      <title></title>
      <script></script>
      
      ...

      <link rel="import" href="../paper-slider/paper-slider.html">
      <link rel="import" href="daum-map.html">
    </head>
    ```

1. 다음으로 앞에서 레이아웃 변경시에 추가한 좌측, 컨트롤패널을 위한 영역에 사용자가 조작할 수 있는 컨트롤을 `paper-slider` 를 사용하여 구현합니다.

    ```
    <core-label>
      Latitude
      <paper-slider id="latitude" min="33.1234" max="38.6154" value="33.4507" step="0.001" immediateValue="0.001"></paper-slider>
    </core-label>
    <br/>
    <core-label>
      Longitude
      <paper-slider id="longitude" min="125.020" max="131.154" value="126.570" step="0.001" immediateValue="0.001"></paper-slider>
    </core-label>
    ```




