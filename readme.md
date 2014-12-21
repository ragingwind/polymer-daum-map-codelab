# Polymer: Create a reusable Web Components with Daum Map

재사용 가능한 Web Components 를 만들기 위해서는 잘 정의된 인터페이스와 그 정의된 인터페이스에 대한 문서가 같이 제공되어야 합니다. 가장 좋은 방법은 Web Component 와 같이 문서가 배포되는 것입니다. Polymer 에서는 여러분들이 쉽게 Web Component 개발뿐 아니라 문서화를 할 수 있도록 [seed element](https://github.com/PolymerLabs/seed-element) 라는 boilerplate 프로젝트를 제공합니다. 프로젝트에는 실제 Web Component 외에 문서와 데모 페이지를 만들 수 있도록 자주 사용되는 파일과 의존성 패키지 설정을 같이 제공합니다. 

이번 코드랩에서는 seed element 를 활용해서 재사용 가능한 Web Component 개발 과정을 경험해보는 시간을 갖도록 하겠습니다. 과정은 크게 3 가지로 구분되며 아래와 같습니다.

1. Web Component 구현, 이번 코드랩에서는 다음맵 API 를 사용하도록 하겠습니다.
1. Github 의 gh_pages 사용하여 문서와 데모페이지 배포
1. Bower 에 패키지 등록, 이번 코드랩에서는 명령어의 사용방법과 데모를 통해서만 패키지 등록 방법에 대해서 알아보겠습니다. 실제 등록시에는 Web Component 의 이름이 중복되어서는 안되기 때문입니다. 만약 실제로 등록을 원하시면 패키지 이름을 변경해서 시도해보시면 됩니다.

### Steps

## 0. Prerequisites

이 Codelab 은 Polymer 에 대한 기초적인 지식이 있는 개발자를 대상으로 하고 있습니다. CLI 환경에서 작업이 익숙하거나 Node.js 로 만들어진 도구 사용에 무리가 없으셔야 합니다. 실제 Polymer APIs 와 attributes 등에 대한 자세한 설명이 없습니다. 충분한 설명을 위해서는 [Polymer Polytechnic 의 Codelabs](http://goo.gl/1WeU2v) 과정을 참고하세요.

Codelab 에서는 Yeoman 을 사용하여 Seed element 와 Github page 를 생성하고 있습니다. 따라서 아래 도구들의 설치가 미리 설치되어 있어야 합니다. 자료가 작성된 환경은 Mac OSX 입니다.

- git 과 github 계정 사용 가능
- 다음맵 API 를 받을 수 있는 다음 계정
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

## 2. Preview the Component

선호하시는 간단한 웹서버를 구동해서 생성된 component 를 위한 페이지를 확인합니다. 
추가로 생된된 demo.html, index.html 으로 컴포넌트의 정보를 확인할 수 있습니다.

index.html 은 [core-component-page](https://github.com/Polymer/core-component-page) 에서 [JSDoc](http://en.wikipedia.org/wiki/JSDoc) 스타일로 작성된 주석을 읽어 들여서 문서를 문서를 만들어 줍니다. 실제로 외부에 노출되는 인터페이스의 경우 주석을 추가해서 외부의 개발자가 사용하기 쉽도록 해주어야 합니다. Yeoman 에서 생성된 seed element 에는 JSDoc 의 boilerplate 주석이 포함되어 있습니다.

서버는 반드시 `daum-map` 의 상위 경로, `daum-map/components`, 즉 components 들이 설치된 곳을 웹서버의 루트로 실행되어야 component 에서 상대경로로 Polymer component 를 참고할 수 있습니다.

```
# node
http-server

# python 
python -m SimpleHTTPServer 8080
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

[그림]

## 4. Change LatLng of Daum Map

이제 외부에서 `<daum-map>` component 로 데이터를 셋팅하면 넘겨진 값으로 다음맵을 조작하는 코드를 구현해보도록 하겠습니다. 여기에서는 Polymer 의 observing properties 방법중에서 가장 쉽게 property 변경값을 처리할 수 있는 [Changed Watcher](http://goo.gl/YGwbSv) 를 사용하겠습니다. 따라서 `propertyNameChanged` 형태로 method 를 선언하고 각 method 를 구현하도록 하겠습니다.

맵에서 가장 기본적인 동작인 위도/경도 값을 이용해서 맵의 중앙값을 조작해서 보여지는 맵의 위치를 바꾸어 보겠습니다. 먼저 `demo.html` 에 값을 조작할 수 있는 컨트롤을 배치하겠습니다. 컨트로은 `paper-slider` 를 사용하고 각 컨트롤을 구분하기 위해서 `core-label` 를 사용하도록 하겠습니다.

1. 이후 좀 더 보기 쉬운 화면을 위해서 `demo.html` 의 레이아웃을 번경하겠습니다. 제목과 좌측에는 컨트롤 패널을 우측에는 다음맵이 보여지도록 조정하였습니다.

    ```
    <h2>Daum Map based on Web Components</h2>
    <div horizontal layout>
      <div vertical layout style="width:406px;margin-right:26px">
      </div>
      <div flex>
        <daum-map apikey="a043a4e407075ed3781cc6bdeef73586867914e4" width="400px" height="400px" level="10"></daum-map>
      </div>
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

1. 다음으로 앞에서 레이아웃 변경시에 추가한 좌측, 컨트롤 패널을 위한 영역에 사용자가 조작할 수 있는 컨트롤을 `paper-slider` 를 사용하여 구현합니다. 아래처럼 구현한 코드를 이전에 구현한 `demo.html` 안의 레이아웃 코드에 삽입합니다.

    ```
    <div vertical layout style="width:406px;margin-right:26px">

      <core-label>
        Latitude
        <paper-slider id="latitude" min="33.1234" max="38.6154" value="33.4507" step="0.001" immediateValue="0.001"></paper-slider>
      </core-label>
      <br/>

      <core-label>
        Longitude
        <paper-slider id="longitude" min="125.020" max="131.154" value="126.570" step="0.001" immediateValue="0.001"></paper-slider>
      </core-label>
      </br>

    </div>
    ```
    - `immediateValue` 값을 사용합니다.

1. `paper-slider` 크기 조정을 위한 간단한 style 을 `<head>` 에 추가합니다.

    ```
    <style>
      paper-slider {
        width: 100%;
      }
    </style>
    ```

1. 선언된 `paper-slider` 에서 사용자가 슬라이드바를 변경할때 발생하는 이벤트를 받아서 변경된 값을 `<daum-map>` 에 전달 할 수 있는 코드를 구현하겠습니다. 먼저 `demo.html` 에 `<script>` 블럭을 추가해서 아래 코드를 추가합니다. 
  
    ```
    function bindEventHandler(selector, event, fn) {
      var e = document.querySelectorAll(selector);
      for (var i = 0; i < e.length; ++i) {
        e[i].addEventListener(event, fn);
      };
    }

    window.addEventListener('polymer-ready', function() {
      // Keep daum map reference
      window.daumMap = document.querySelector('daum-map');

      // Bind event handler for paper-slider
      bindEventHandler('paper-slider', 'immediate-value-change', function(e) {
        daumMap[e.target.id] = e.target.immediateValue;
      });
    });
    ```

    - `bindEventHandler` 는 `paper-slider` 에 특정 이벤트를 추가시켜 주는 유틸함수입니다.
    - [The polymer-ready event](http://goo.gl/7uJ5Hc) 를 참고하세요
    - `window.daumMap` 에 생성된 `<daum-map>' instance 를 저장합니다.
    - `immediate-value-change` 는 유저가 슬라이드를 움직이는 동안에 이벤트가 지속적으로 발생합니다.
    - `paper-slider` element 의 id 와 `<daum-map>` property 의 이름을 같게 해서 바로 데이터를 넘겨주도록 했습니다.

1. `daumMap[e.target.id] = e.target.immediateValue;` 코드를 통해서 넘어오는 값을 처리하는 changed watcher 를 Polymer({}) 에 추가합니다. 내부 구현에는 Daum Map 에서 제공하는 `getCenter / setCenter` 를 이용해서 맵의 위치를 변경합니다. 

    ```
    latitudeChanged: function(oldLatitude, newLatitude) {
      var center = this.map.getCenter();
      this.map.setCenter(new daum.maps.LatLng(newLatitude, center.getLng()));
    },

    longitudeChanged: function(oldLongitude, newLongitude) {
      var center = this.map.getCenter();
      this.map.setCenter(new daum.maps.LatLng(center.getLat(), newLongitude));
    }
    ```

    - Changed Watcher 는 `propertyName + Changed` 로 정의합니다.

이제 슬라이드를 조작해서 맵의 센터가 변경되는지 확인합니다.

[그림]

## 5. Change Level of Daum Map 

맵의 확대 수준을 바꾸어 보겠습니다. 이전 단계에서와 같이 먼저 컨트롤을 배치하고 변경되는 값을 `<daum-map>' 의 인터페이스에게 전달합니다. 

1. `paper-slider` 를 이용해서 컨트롤을 추가하겠습니다. 아래 코드를 이전에 추가한 `demo.html` 안의 레이아웃코드내에 추가합니다.

    ```
    <div vertical layout style="width:406px;margin-right:26px">

      ...

      <core-label>
        Level
        <paper-slider id="level" min="1" max="10" value="3" immediateValue="1" snaps="true" step="1" pin="true"></paper-slider>
      </core-label>
      <br/>

    </div>
    ```

1. 추가한 컨트롤의 데이터 변경값은 이전에 추가한 코드로 같이 처리가 됩니다 따라서 별도로 이벤트를 처리하는 코드는 추가하지 않습니다. 바로 changed watcher 를 `daum-map.html` 에 추가합니다.

    ```
    levelChanged: function(oldLevel, newLevel) {
      this.map && this.map.setLevel(newLevel);
    },
    ```

## 6. Change Width / Height of Daum Map

마지막으로 맵 컨테이너의 크기를 변경하는 컨트롤과 이벤트를 처리하는 코드를 추가합니다. 이전 작업과 마찬가지로 컨트롤과 changed watcher 를 추가합니다.

1. 먼저 아래 `paper-slider` 를 사용하는 컨트롤을 `demo.html` 의 레이아웃내에 추가합니다.

    ```
    <div vertical layout style="width:406px;margin-right:26px">

      ...

      <core-label>
        Map Width
        <paper-slider id="width" min="400" max="1024" value="400" immediateValue="24" snaps="true" step="24" pin="true"></paper-slider>
      </core-label>
      <br/>
      <core-label>
        Map Height
        <paper-slider id="height" min="400" max="1024" value="400" immediateValue="24" snaps="true" step="24" pin="true"></paper-slider>
      </core-label>

    </div>
    ```
2. 다음으로 changed watcher 코드를 `daum-map.html` 의 이전에 추가한 `levelChanged` 다음에 추가합니다.

    ```
    widthChanged: function(oldWidth, newWidth) {
      this.$.map.style.width = this.pixelize(newWidth);
      this.map && this.map.relayout();
    },

    // Run when height has been changed
    heightChanged: function(oldHeight, newHeight) {
      this.$.map.style.height = this.pixelize(newHeight);
      this.map && this.map.relayout();
    },

    // Make a width with 'px'
    pixelize: function(s) {
      return s + (!/px^/.test(s) ? 'px' : '');
    },
    ```
    - `pixelize` 는 `px` 를 붙여주는 간단한 함수입니다.
 
이제 맵에서 맵컨테이너의 크기가 변경되는 것을 확인해봅니다.

## 9. Mouse event Delegate

다음맵에서 제공하는 여러 이벤트 중에서 간단한 클릭 이벤트를 인터페이스로 만들어 보겠습니다. 먼저 `daum-map.html` 의 `ready` method 에 클릭이벤트를 처리하도록 코드를 추가하겠습니다. 사용자가 맵을 클릭하면 다음맵은 이벤트를 발생시키고 다시 이 component 내에서 `map-click` 이벤트로 다시 외부에 발생시킵니다.

1. 먼저 인터페이스 설명을 위해서 이벤트를 fire 시키는 method 를 Polymer({}) 에 추가합니다. 

    ```
    fireMapClick: function(e) {
      this.fire('map-click', e);
    },
    ```
  
1. 다음맵에 클릭 이벤트에 대한 event listenenr 를 추가시키고 위에서 추가한 method 를 호출하도록 코드를 구현합니다.  
   
    ```
    window.addEventListener('polymer-ready', function() {

      ...

      daumMap.addEventListener('map-click', function(e) {
        setLatLng(e.detail.latLng.getLat(), e.detail.latLng.getLng());
      });
    });
    ```
    - `click` 은 `polymer-element` 에 기본으로 제공되는 이벤트입니다. `map-click` 으로 구분했습니다.

1. 먼저 클릭 이벤트의 내용을 보여줄 라벨(id="click-latlng")을 맵컨테이너 위에 추가합니다.
    ```
    <div flex>
      <span id="click-latlng"></span>
      <daum-map ... ></daum-map>
    </div>
    ```

1. 다음 라벨의 내용을 변경하는 간단한 함수를 `demo.html` script 블럭에 추가합니다.

    ```
    function setLatLng(lat, lng) {
      document.querySelector('#click-latlng').textContent = [lat, lng].join(' ');
    };
    ```

1. `demo.html` 내에 `map-click` 이벤트를 처리할 코드를 추가합니다. 넘어온 event argument 의 detail 에는 다음맵에서 넘겨준 event 정보가 있습니다. 그 중에서 latLng 를 사용하여 현재 클릭한 곳의 위도/경도 값을 알 수 있습니다. 그 값을 앞에서 추가한 `setLatLng` 함수를 사용해서 출력합니다.

    ```
    daumMap.addEventListener('map-click', function(e) {
      setLatLng(e.detail.latLng.getLat(), e.detail.latLng.getLng());
    });
    ```

1. 맵이 처음 로딩될 때 초기값을 표여줄 함수를 이전에 추가한 코드 아래에 바로 추가합니다.

    ```
    setLatLng(document.querySelector('#latitude').value, document.querySelector('#longitude').value);
    ```

이제 맵을 클릭하면 상단의 위도/경도 값이 바뀌는지 확인합니다.

[그림]

## 8. Component page for Developers

위의 코드를 제대로 구현했다면 데모에서 Component 가 정상적으로 동작하는지 확인 가능합니다. 재사용 가능한 Web Component 를 만들었다면 이제 다른 개발자들이 쉽게 사용할 수 있도록 문서를 위한 작업을 하도록 하겠습니다. Polymer 에서는 위에서 언급한 것과 같이 core-component-page](https://github.com/Polymer/core-component-page) 와 JSDoc 을 사용합니다. 따라서 `daum-map.html` 의 인터페이스에 JSDoc style comment 를 추가한다면 손쉽게 문서를 작성 할 수 있습니다.

이제 다시 Component page (http://localhost:8000/daum-map/) 로 돌아가겠습니다. 현재는 `daum-map.html` 상단의 예제를 제외하고는 인터페이스에 대한 설명이 없기때문에 별다른 설명은 존재하지 않습니다. 

1. 먼저 예제를 수정합니다. `daum-map.html` 헤더에 존재하는 Example 을 `demo.html` 에서 사용하는 예제로 교체합니다.

    ```
    ##### Example
        <daum-map apikey="YOUR-APIKEY" width="400px" height="400px" level="10"></daum-map>
    ```

    - apikey 를 문서에서는 제거하는게 좋습니다.

1. property 에 주석 추가합니다. property 에는 attribute 와 type 그리고 간단한 용도에 대해서 설명합니다. 아래는 apikey 에 추가한 것입니다. 외부에 노출되는 다른 property 의 경우에도 모두 주석을 붙입니다.

    ```
    /**
      * The `apikey` attribute sets an apikey for fetching and using library
      *
      * @attribute apikey
      * @type string
      */
    apikey: '',
    ```

1. 외부로 전달되는 이벤트에 대한 주석을 추가해 보겠습니다. `fireMapClick` method 위에 아래 주석을 추가합니다. `@event` 를 이용해서 이벤트를 설명할 수 있습니다. 

    ```
    /**
     * The `map-click` event is fired whenever we
     * call fireLasers.
     *
     * @event map-click
     * @param {Object} event
     *   @param {Object} event.detail.latLng. A Lat, Lng of point
     *   @param {Object} event.detail.point. A point of click
     */

    fireMapClick: function(e) {
      this.fire('map-click', e);
    },
    ```

component page 에 접속해서 문서의 설명이 보여지는지 확인합니다.

[그림]


## 9. Export the Component page to Github page

만들어진 component 를 github 에 배포하고 component page 는 github 의 gh_pages 에 배포하도록 하겠습니다. 

1. github 에 사용할 repository 를 만드세요. 그 다음 프로젝트를 push 합니다. 

    ```
    git init
    git remote add origin https://github.com/[YOUR-GITHUB-ID]/daum-map.git
    git add -A 
    git commit -a -m 'daum-map component'
    git push origin master
    ```
    - [Creating reusable Polymer elements with Yeoman seed-element generator:Push your project](http://goo.gl/Rb8fpI) 동영상을 참고하세요.

1. gh_pages 에 배포하도록 하겠습니다. Yeoman 에서는 간단한 명령을 사용해서 바로 gh_pages 에 배포할 수 있습니다. 아래 명령을 사용해서 배포하도록 하겠습니다. 명령을 실행하면 Yeoman 은 입력받은 github id 와 repogitory 이름을 사용해서 clone 한 다음 필요한 패키지를 처리하고 gh_pages 로 component page 를 push 합니다.

    ```
    yo polymer:gh
    ```
    - [Creating reusable Polymer elements with Yeoman seed-element generator:Deploy landing page](http://goo.gl/p4vg4v) 동영상을 참고하세요

1. 다음맵 component page 에 접속해보겠습니다. 접속 주소는 github id (ragingwind) 와 repogitory (daum-map) 이름에 맞게 수정하세요. 

    ```
    http://ragingwind.github.io/daum-map
    ```

## 10. Publish package to bower

bower 의 [register](http://goo.gl/zr3Qgq) 를 사용해서 bower 패키지에 등록해보고록 하겠습니다. 명령은 아래와 같습니다.

```
bower register <my-package-name> <git-endpoint>
```

등록하기전에는 `bower.json` 를 version 을 포함한 properties 를 수정하고 github 에 원하는 버전이 push 가 되었는지 확인합니다. 이후 아래 명령을 사용하여 register 해보도록 하겠습니다. %주의% 동일한 이름을 사용하면 안됩니다.

```
bower register daum-map https://github.com/ragingwind/daum-map.git
```

이제 `bower info` 명령을 사용해서 등록한 패키지의 정보를 쿼리 합니다.

```
bower register daum-map https://github.com/ragingwind/daum-map.git
```

이제 등록된 패키지는 `bower install daum-map` 으로 여러 개발자들이 사용할 수 있습니다.

## 11. That's all folks

이제 과정들을 모두 끝냈습니다. 모두 수고하셨습니다. 이제 Polymer 를 사용해서 여러 다른 개발자들이 재사용 할 수 있는 component 를 만들고 문서화를 위해서 작업을 하고 개발시에 참고할 수 있는 문서와 데모 페이지를 포함한 landing page(component page) 를 작성하고 배포하는 방법을 익히셨습니다. 앞으로도 많은 재사용가능한 component 를 작성하시고 공유해주세요

## References

- [daum-map](http://goo.gl/lQaAzs)
- [Daum 지도 Web API Documentation](http://goo.gl/4EVJ8E)
- [ragingwind/daum-map](http://goo.gl/jd3dsU)
- [PolymerLabs/seed-element](http://goo.gl/8aRU4z)
- [Creating reusable elements - Polymer](http://goo.gl/rrfpMh)
- [만드세요 Polymer - GDG Korea, 시작하세요 Polymer OCT, 2014 - Google Slides](http://goo.gl/4NYXdq)
