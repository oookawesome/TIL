## Angular 학습 일지

### 3장 앵귤러 시작
- angular CLI 명령을 통해 컴포넌트 등을 쉽게 생성할 수 있다.
- $ng generate component {name} 치면 html, scss, tc, spec.tc 등을 바로 만들어준다
- @NgModule 데코레이터는 특정 모듈(우리 경우에는 우리앱)의 모든 임포트, 선언 등을 한곳에서 관리하는 역할을 한다.
  - provider : injector가 받는 주입받는 서비스들
  - declaration : 우리 모듈에 속하는 다른 컴포넌트들
  - imports : 라이브러리와 같이, 우리 모듈에 속하지 않는 다른 컴포넌트들
  - bootstrap : 부트스트랩 컴포넌트 (메인 함수 역할)

### 4장 컴포넌트
- component 생성하면 @Component 붙어있는 ts파일 생성됨
  - selector : HTML사용시 해당 컴포넌트가 가지게될 이름(태그명)
  - template : 해당 컴포넌트 모양을 인라인 HTML형식으로 지정
  - templateUrl : 해당 컴포넌트의 모양을 외부 템플릿으로 지정
  - styles : 해당 컴포넌트의 디자인을 인라인 형식으로 지정
  - styleUrls : 해당 컴포넌트의 디자인을 외부 CSS파일로 지정
- 초기 데이터 설정 시 생성자 사용
- @Input 이용해 외부에서 컴포넌트 내부로 데이터를 전달받는 것을 정의할 수 있음

### 5장 표현식
- 앵귤러는 HTML 파일 내에서 자바 스크립트 표현식을 사용할 수 있음
- {{}} 형식으로 표현식 사용
  - 내부에 +,-,=== 같은 연산자 사용가능
  - 컴포넌트 내부의 변수와 함수를 참조하는 것이 가능
  - 타입 스크립트 사용가능
- 파이프(|) 사용
  - 파이프는 데이터의 표현 형식을 수정하는 연산자
  - 내장 파이프를 제공함 (json, date...)
  - ex. {{expression | json}}
  - @Pipe 이용해 사용자정의 파이프 구현 가능

### 6장 데이터 바인딩
- 데이터 바인딩은 애플리케이션의 데이터를 UI요소와 연결하는 것. 모델에서 데이터가 변경되면, 웹 페이지가 자동으로 업데이트 된다
  - 삽입식 : 이중중괄호({{}})를 사용해 표현식을 작성  
  ```
  <img src = "{{imgUrl}}" />
  ```
  - 프로퍼티 바인딩 : HTML 요소의 프로퍼티를 설정해야 할 때 프로퍼티 바인딩 사용  
  ```
  <img [src]="myValue">
  ```
  - 클래스 바인딩 : CSS 스타일 태그를 HTML 요소에 바인딩할 때 사용. 표현식의 결과가 true인 경우에 클래스가 할당됨  
  ```
  <div [class.nameHere]="true"></div>
  ```
  - 스타일 바인딩 : HTML 요소에 인라인 스타일을 지정할 때 사용. 클래스 바인딩과 유사하지만, 접두어로 style이 붙음  
  ```
  <p [style.styleProperty] = "assignment"></p>
  ```
  - 이벤트 바인딩 : 클릭, 키 입력, 마우스 이동과 같은 사용자 입력을 처리할 때 사용. HTML 이벤트 속성에서 on 접두사가 제거되고, 이벤트를 괄호(())로 감쌈  
  ```
  <button (click)="myFunction()">button</button>
  ```
- 양방향 바인딩 : ngModel을 사용해 변경사항을 감지하고, 값을 업데이트 함  
```
<input [(ngModel)] = "myValue">
```

### 7장 내장 디렉티브
- 디렉티브는 템플릿 데이터와 HTML요소의 동작방식을 정의
- 앵귤러 내장 디렉티브
  - ngFor : iterable객체 내의 각 항목에 대한 사본 생성  
  ```
  <div *ngFor="let person of people"></div>
  ```
  - ngIf : 해당 값이 true인 경우에만 DOM에 추가, false이면 DOM에서 제거됨  
  ```
  <div *ngIf="person"></div>
  ```
  - ngSwitch/ngSwitchCase/ngSwitchDefault : switch문을 구성함. 값이 아무것도 일치하지 않으면 DOM 생성하지 않음  
  ```
  <div [ngSwitch]="timeOfDay">
    <span [ngSwitchCase]="'morning'">Morning</span>
    <span [ngSwitchCase]="'afternoon'">Afternoon</span>
    <span [ngSwitchDefault]="'daytime'">Evening</span>
  ```

### 8장 사용자 정의 디렉티브
- @Directive 애노테이션을 통해 사용자 정의 디렉티브를 구성
