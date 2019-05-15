



## function *

generator function 정의문이다. 이 함수는 generator 객체를 반환한다.

```js
function * generator(i){
  yield i
  yield i+10
}
const gen = generator(10)
console.log(gen.next().value) // 10
console.log(gen.next().value) // 20

```

Generator는 빠져나갔다가 나중에 다시 돌아올 수 있는 함수이다. 이때 컨텍스트값 ( 변수값 )은 출입 과정에서 저장된 상태로 남아있다.

Generator 함수는 즉시 실행되지 않고, 대신 함수 실행을 위한 <code>iterlator</code> 객체를 반환한다. <code>iterlator</code> 객체의 <code>next()</code> 메소드를 호출하면 Generator가 실행되어 <code>yield</code> 문을 만날 때까지 진행하고, 해당 <code>yield</code> 문이 명시하는 값을 반환한다. 만약 <code>yield * </code> 표현식을 마주 할 경우 다른 Generator 함수가 위임되어 진행된다.

이후 <code>next()</code> 메소드가 다시 호출되면 진행이 멈췄던 위치에서 재실행 된다.

```js
function* anotherGenerator(i){
  yield i+1
  yield i+2
  yield i+3
}

function* generator(i){
  yield i
  yield* anotherGenerator(i)
  yield i+10
}

const gen = generator(10)
console.log(gen.next().value) // 10
console.log(gen.next().value) // 11
console.log(gen.next().value) // 12
console.log(gen.next().value) // 13
console.log(gen.next().value) // 20
```

만약 <code> next() </code> 함수가 인자값과 함께 호출할 경우, **진행을 멈췄던 위치의 <code>yield</code> ** 문을 <code>next()</code> 메서드에서 받은 인자값으로 치환하고 그 위치에서 다시 실행하게 된다.

```js
function* generator(){
  console.log(yield i) // yield i 자체가 'test'로 치환 , output : 'test'
}
const gen = generator()
gen.next('test')
```

Generator는 생성자로서 사용될 수 없다.

```json
function* generator(){}

const obj = new generator // 'TypeError : generator is not constructor'
```

