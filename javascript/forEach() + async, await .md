

### 

_다음글은  [여기](<https://codeburst.io/javascript-async-await-with-foreach-b6ba62bbf404>) 를 참고한 글 입니다_

## forEach method에 async, await 활용

코딩을 하다가 forEach 다음에 어떠한 작업을 수행하게 하고 싶었다. 

```js
const waitFor = (ms) => new Promise( r=> setTimeout(r,ms))
const arr = [1,2,3]
arr.forEach( async(val) => {
  await waitFor(50)
  console.log(val)
})
console.log('please')
```

이 코드를 실행시키면

```bash
$ node index.js
$ please
$ 1
$ 2
$ 3
```

forEach 문을 수행한 뒤에 밑에있는 console.log 값을 찍고 싶은데 'please' 값이 먼저 찍히고 반복문이 수행되었다.

해결방법은 forEach 반복문을 asyncForEach(예시) 함수로 감싸서 프로미스를 리턴하게 하게하는 방법이다.

우선 asyncForEach 함수를 만들어보자 

```js
async function asyncForEach(array, callback){
  for(let i=0; i<array.length; i++){
    await callback(array[i])
  }
} 
```

크게 감쌀 함수를 작성 했으니, callback 함수를 다시 작성하여 넣어보자

```js
const start = async () =>{
  await asyncForEach(arr, async (num) => {
  await waitFor(50)
  console.log(num)
})
console.log('please')
}
start()
```

이제 실행시킨다면?

```bash
$ node index.js
$ 1
$ 2
$ 3
$ please
```

성공이다! 앞으로 자주 사용할 것 같다

