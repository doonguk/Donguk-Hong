## 1. 프로토타입 객체

<br/>

> 이 글은 [여기](https://poiemaweb.com/js-prototype) 를 공부하여 정리 하였습니다.

<br/>

Java, C++과 같은 클래스 기반 객체지향 프로그래밍 언어와 달리 자바스크립트는 프로토타입 기반 객체지향 언어이다. 클래스 기반 객체지향 프로그래밍은 클래스를 정의하고 이를 통해 객체를 생성한다.

하지만 프로토타입 기반 객체지향 프로그래밍은 클래스 없이도 객체를 생성할 수 있다.( + ES6 에서 클래스 추가 됐음!)

자바스크립트의 모든 객체는 부모역할을 하는 객체와 연결되어 있다. 그리고 마치 객체 지향의 상속개념과 같이 부모객체의 메소드와 프로퍼티를 상속받아 사용할 수 있게 한다. 이러한 **부모 객체를 Prototype Object 또는 줄여서 Prototype** 이라 한다.

```javascript
const testObj = {
	myField : 'good'
}
console.log(student.hasOwnProperty('myField')) // true
```

testObj 는 hasOwnProperty method를 갖고있지 않지만 오류가 뜨지않는다. 프로토타입 객체의 메소드를 사용할 수 있기 때문!

**자바스크립트의 모든 객체는 [[Prototype]] 이라는 인터널 슬릇(Internal slot)을 가진다**

[[Prototype]] 의 값은 null 또는 Prototype 객체 이다. 자식 객체에서 __ proto __ 로 접근할 수 있다.__ proto __ 에 접근하면 내부적으로 Object.getPrototypeOf 메소드가 호출되어 프로토타입 객체를 반환한다.

```js
const testObj = {}
console.log(testObj.__proto__ === Object.getPrototypeOf) // true
```

 

## 2. [[Prototype]] vs prototype property

함수를 포함한 모든객체는 자신의 프로토타입 객체를 가리키는 [[Prototype]] 인터널 슬릇을 갖으며 상속을 위해 사용된다. 단 함수는 인터널 슬릇을 갖지만 추가적으로 prototype property도 소유한다.

```js
function Person(name){
	this.name = name
}
const foo = new Person('donguk')
console.dir(Person) // prototype property 가짐
console.dir(foo) // prototype property 갖고있지 않아!
```

##### [[Prototype]]

- 함수를 포함한 모든 객체가 소유한 인터널 슬릇

- 객체의 입장에서 자신의 부모역할 을 하는 프로토타입 객체를 가리키며, 함수객체의 경우 Function.prototype을 가리킨다.

  ```js
  console.log(Person.__proto__ === Function.prototype	) // true
  ```

##### prototype property

- 함수객체만 가지고 있는 프라퍼티다.

- **함수객체가 생성자로 사용될 때 이 함수를 통해 생성될 객체의 프로토타입 객체를 가리킨다**

  ```js
  console.log(Peroson.prototype === foo.__proto__) //ture
  ```



## 3. constructor property

프로토타입 객체는 constructor property를 갖는다. 이 contructor 프라퍼티는 자신을 생성한 객체를 가리킨다.

```js
function Person(name){
	this.name = name
}
const foo = new Person('donguk') // Person 생성자 함수에의해 생성된 객체

//foo obj를 생성한 obj는 Person() 생성자 함수이다
console.log(foo.constructor === Person) // true
//Person 생성자 함수에의해 상성된 객체를 생성한 객체는 Person() 생성자 함수이다.
console.log(Person.prototype.contructor === Person) // true
//Person() 생성자함수를 생성한 객체는 Function() 생성자 함수이다.
console.log(Person.constuctor === Function)
```

## 4. Prototype chain

자바스크립트는 특정 객체의 메소드나 프라퍼티에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티나 메소드가 없다면 인터널슬릇이 링크를 따라서 해당객체의 부모역할을 하는 프로토타입 객체의 메소드나 프라퍼티를 검색하게 된다. 이것을 프로토타입 체인이라고 한다.

```js
const student = {
  name : 'donguk'
}

console.log(student.hasOwnProperty('name')) // true
```

student object 는 hasOwnProperty 라는 메소드를 갖고 있지 않지만 student의 __ proto __ 링크를 따라서 부모 프로토타입객체 ( Object.prototype ) 이 갖고 있는 hasOwnProperty 메소드를 호출한 것이다.

```js
const student = {
  name : 'donguk'
}

console.log(student.hasOwnProperty('name')) // true
console.log(student.__proto__ === Object.prototype ) // true
console.log(Object.prototype.hasOwnProperty('hasOwnProperty')) // true
```


### 4-1 객체 리터럴 방식으로 생성된 프로토타입 체인

> _리터럴에 관한 내용은 [여기](<https://asfirstalways.tistory.com/21>) 를 참고하였습니다._
>
> 잠깐! 리터럴 방식이란? 위키백과에 검색을 해보면 '컴퓨터 과학에서 리터럴이란, 소스코드의 고정된 값을 대표하는 용어이다' 그럼 상수와 비슷한 개념인가?
>
> 일반적으로 상수와 대조적인 말로 변수가 있다. 리터럴은 상수와 변수처럼 어떠한 값을 명칭하는게 아니라 변수및 상수에 저장되는 "값 자체"를 의미한다. ( 상수와 변수는 값을 표현 하지만 리터럴은 값 그자체를 의미 ) 함수 리터럴, 문자열 리터럴, 배열 리터럴 처럼 언어의 한 요소로서 리터럴이라고 불린다.
>
> 추가적으로  <u>데이터를 표현하는 방식</u>을 리터럴 이라고 한다. 일반적으로 OOP 에서는 리터럴 표기법을 지원한다.
>
> 리터럴 표기법이란, 변수를 선언함과 동시에 그 값을 지정해주는 표기법이다.
>
> ```java
> const foo = 3
> const var = { foo : 1 }
> ```
>
> 리터럴 표기법은 비정규적인 방법이 아니다. 코드가 더 짧기 때문에 자바스크립트 인터프리터의 해석량도 줄어들며, 성능저하도 불러오지 않는다.

객체 리터럴로 생성된 객체는 내부적으로 Object() 생성자 함수를 사용하여 생성된 것이다. Object() 생성자 함수는 함수이기 때문에 일반 객체와 다르게 prototype 프라퍼티를 갖는다.

```js
const student = {
  name : 'donguk'
}

console.log(student.__proto__ === Object.prototype) // true
console.log(Object.prototype.constructor === Object ) // true
console.log(Object.__proto__ === Function.prototype) // true
console.log(Function.prototype.__proto__ === Object.prototype) //true
```


