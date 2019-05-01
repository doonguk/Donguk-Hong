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

