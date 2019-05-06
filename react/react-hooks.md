## React Hooks

> 이 글은 [velopert님의 hooks관련 정리 포스트](<https://velog.io/@velopert/react-hooks>) 를 참고하여 공부하였습니다.

React Hooks 는 React v16.8에 도입된 새로운 기능이다. Hooks 는 함수형 컴포넌트에서 상태관리를 할 수 있게 도와주는 useState, 컴포넌트가 생성되거나, 업데이트 될때 호출되는 useEffect 등 기능을 제공하여 기존의 class형 컴포넌트 에서만 가능했던 기능들을 함수형 컴포넌트에서도 사용 할 수 있게 도와준다.

### 1. useState

함수형 컴포넌트에서도 state 값을 설정 할 수 있게 해준다.

```js
import React, { useState } from 'react'

const HookTutorial = () => {
  const [name, setName] = useState(' ')
  const handleChange = (e) => {
    setName(e.target.value)
  }
  return(
    <div>
      <p>My Name is {name}</p>
      <input name="name" onChange={handleChange}/>
    </div>
  )
}
export default HookTutorial
```

useState는 상단의 import 구문을 통해서 불러온다. 사용법은

```js
const [name, setName] = useState(' ')
```

와 같이 사용한다. 이 문법은 **배열 비구조화 할당** 이다

- 잠깐, 배열비구조화 할당이란?

  ```js
  function test(){
    return ['mother','father','brother']
  }
  const [m, f, b] = test()
  console.log(m, f, b) // mother, father, brother
  ```

  객체의 비구조화 할당과 비슷한 개념!

다시 돌아와서 useState는 파라미터로 state의 기본값을 받는다. 그리고 return 되는 값이 배열인데, 이 배열의 첫번째 원소가 상태값이고 두번째 원소가 상태를 설정하는 함수이다. 이 함수에 파라미터로 설정 값을 넣어주면 전달받은 파라미터로 상태값이 바뀌게 되고 컴포넌트가 리렌더링 된다.

#### 1.1 useState 여러번 사용하기

```js
import React, { useState } from 'react'

const HookTutorial = () => {
  const [name, setName] = useState(' ')
  const [subName, setSubName] = useState( ' ')
  const handleChange = (e) => {
    const { value } = e.target
    e.target.name === 'name' ? setName(value) : setSubName(value)
  }
  return(
    <div>
      <p>My Name is {name}</p>
      <p>My SubName is {subName}</p>
      <input name="name" onChange={handleChange}/>
      <input name='subName' onChange={handleChange}/>
    </div>
  )
}

export default HookTutorial
```

한개의 useState로는 하나의 state만 설정 할 수 있기 때문에 여러개의 state를 관리하기 위해서는 useState를 여러개 사용하면 된다.

### 2. useEffect

useEffect는 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정 할 수 있는 Hooks 이다. Lifecycle 함수인 componentDidMount() 함수와 componentDidUpdate() 함수를 합친 느낌이다.

```js
import React, { useState, useEffect } from 'react'

const HookTutorial = () => {
  const [name, setName] = useState(' ')
  const [subName, setSubName] = useState( ' ')
  const handleChange = (e) => {
    const { value } = e.target
    e.target.name === 'name' ? setName(value) : setSubName(value)
  }
  useEffect(() => {
    console.log('rendering')
    console.log({
      name, subName
    })
  })
  return(...)
}
export default HookTutorial
```

useState와 같이 import 구문으로 불러온다. 개발자도구로 결과를 확인해보면,,

<img width="287" alt="스크린샷 2019-05-06 오전 11 07 05" src="https://user-images.githubusercontent.com/39187116/57209576-ee869e00-7013-11e9-9dba-aecaaeeae066.png">

위와 같이 mount 될때, state 값이 바뀔 때마다 컴포넌트가 useEffect 함수가 호출된다.

**만약에 컴포넌트가 mount 될 때만 실행하고 싶다면?**

```js
useEffect(()=>{ console.log('only Mount')}, [])
```

useEffect 함수의 두번째 파라미터로 비어있는 배열을 전달해주면 된다.

**그럼 state 값이 업데이트 될 때만 실행하고 싶다면?**

```js
useEffect(()=>{ console.log('I'll only watch name state')}, [name])
```

두번째 파라미터에 state( 이 state가 update 될 때만) 값을 전달해주면 된다.

#### 컴포넌트가 unMount 될 때 또는 업데이트 직전에 useEffect 함수를 호출하고 싶다면?

useEffect 함수에 return 값으로 함수를 설정하여 ( 뒷정리 함수라고 한다.) 구현하고 싶은 로직을 넣어주면 된다.

```js
//App.js
import React,{useState} from 'react';
import HookTutorial from "./HookTutorial"

function App() {
  const [ visible, setVisible ] = useState(true)
  const handleVisible = () => {
    setVisible(!visible)
  }
  return (
    <>
      {visible && <HookTutorial/>}
      <button onClick={handleVisible}>Button</button>
    </>
  );
}
export default App;
```

컴포넌트가 언마운트 될 때 수행결과를 확인하기 위해 App.js 에 버튼을 추가하여 visible 관리를 했다.

```js
  //HooksTutorial.js
	useEffect(() => {
    console.log('rendering')
    console.log({
      name, subName
    })
    return ()=>{
      console.log('clean up')
      console.log(name)
    }
  })
```

useEffect 함수도 뒷처리 함수를 return 해 주었다.

개발자 도구로 결과를 확인 해보면 컴포넌트가 unMount 될 때 'clean up' 콘솔과 **업데이트 직전 name 값**이 있는걸 확인 할 수 있다. 하지만! input 요소에 값을 넣어보면, 뒷처리 함수가 계속 호출 된다. 만약 **컴포넌트가 언마운트 될 때만 호출하고 싶다면 useEffect() 함수의 두번째 파라미터로 비어있는 배열을 전달 해 주면 된다. **

### 3. useContext

이부분은 React Context API 를 공부하고 다시 봐야겠다.

### 4. useReducer

useReducer는 useState 보다 컴포넌트에서 더 다양한 상황에 따라 다양한 상태를 업데이트 시켜주고 싶을 때 사용한다.

 _일반적으로 Redux 에서의 액션객체에는 액션이 어떤 타입인지 알려주면 type 필드가 꼭 있어야 하지만 useReducer 에서 사용하는 액션객체는 그러지 않아도 된다. 문자열 심지어 숫자여도 상관 없다._

```js
import React, { useReducer } from 'react'

function reducer(state, action){
  switch(action.type){
    case 'SET_NAME' :
      return {...state, name : 'donguk'}
    case 'SET_SUBNAME' :
      return {...state, subName : 'sub_donguk'}
    default :
      return state
  }
}
const HookTutorial = () => {
  const initialState = {
    name : '',
    subName : ''
  }
  const [ state, dispatch ] = useReducer(reducer, initialState)
  return(
    <div>
      <p>My Name is {state.name}</p>
      <p>My SubName is {state.subName}</p>
      <button onClick={()=>dispatch({type : 'SET_NAME'})}>Set Name</button>
      <button onClick={()=>dispatch({type : 'SET_SUBNAME'})}>Set SubName</button>
    </div>
  )
}

export default HookTutorial
```

useReducer 함수의 첫번째 파라미터로는 reducer 함수, 두번째 파라미터로는 initialState 값을 넣는다. 이 Hook을 사용 했을 경우에는 state 값과 dispatch 함수를 가져오는데 state 값은 현재 상태를 의미하고 dispatch 는 파라미터로 action type 을 받아서 액션을 발생시키는 함수이다. 전체적으로 봤을 때 dispatch(action) 형태로 액션을 발생시키면 reducer 함수가 호출되는 구조이다.

*useReducer 함수의 가장 큰 장점은 컴포넌트 업데이트 로직을 바깥으로 빼낼수 있다는 점이다.*

### 4.1 useReducer 함수로 input 상태 관리하기

```js
import React, { useReducer } from 'react'

function reducer(state, action){
  return(
      {...state, [action.name] : action.value}
  )
}
const HookTutorial = () => {
  const initialState = {
    name : '',
    subName : ''
  }
  const [ state, dispatch ] = useReducer(reducer, initialState)
  const handleChange = (e) => {
    dispatch(e.target)
  }
  return(
    <div>
      <p>My Name is {state.name}</p>
      <p>My SubName is {state.subName}</p>
      <input name='name' value={state.name} onChange={handleChange}/>
      <input name='subName' value={state.subName} onChange={handleChange}/>
    </div>
  )
}

export default HookTutorial
```

useRedcuer 에서는 액션 객체로 무엇이든 될 수 있기 때문에 e.target 값을 액션으로 보냈다. 리듀서에서는 action 으로 e.target 값을 받아서 input 요소를 업데이트 해주었다.



### 5. useMemo 

```js
import React, {useState} from 'react'

const HookTutorial = () => {
  const [number, setNumber] = useState('')
  const [list, setList ] = useState([])
  const handleChange = (e) => {
    setNumber(e.target.value)
  }
  const handleInsert = () => {
    const newList = list.concat(parseInt(number))
    setList(newList)
  }
  const average = (numbers) =>{
    console.log('Calculate.....')
    const sum = numbers.reduce((a,b) => a+b,0)
    return sum/numbers.length
  }
  return(
      <div>
        <input onChange={handleChange}/>
        <button onClick={handleInsert}>등록</button>
        <ul>
          {list.map( (value, index) => (
              <li key={index}>{value}</li>
              )
          )}
        </ul>
        <div>
          <b>평균값 {average(list)}</b>
        </div>
      </div>
  )
}
export default HookTutorial
```

숫자를 입력 받아서 등록 할 경우 숫자들의 평균을 구하는 코드이다. 여기서 만약 input 요소의 값이 바뀐다면 state 가 바뀌게 되고 컴포넌트가 리렌더링 되면서 average 함수도 input 요소의 변경에 따라 계속 재 호출 될 것이다.

useMemo 함수를 사용하면 함수형 컴포넌트에서 연산 관련 작업을 할 때 특정값을 주시함으로써 연산을 최적화 할 수 있다.

```js
const avg = useMemo(()=>average(list), [list])
```

list 값이 변경될 경우에만 average 함수를 호출한다. ( **[ ]로 감싸줘야함 **) useMemo는 렌더링 하는 과정에서 특정 값이 바뀐 경우에만 작업을 수행하고, 바뀐 것이 아니라면 이전에 연산했던 결과를 재사용하는 구조이다.

### 6. useCallback

useCallback은 렌더링을 최적화 하는 상황에서 사용한다. 이 Hook을 사용하면 이벤트 핸들러 함수를 특정 상황에만 렌더링되게 할 수 있다. 위의 코드(useMemo example) 에서 handleChange 함수와 handleInsert 함수는 컴포넌트가 생성될 때 부터, state 값이 업데이트 될 때 마다 다시 렌더링 된다. 함수의 개수가 적으면 상관 없지만 만약 많아 진다면 성능 최적화 면에서 좋지 않다.

```js
  const handleChange = useCallback((e)=>{
    setNumber(e.target.value)
  },[])
  const handleInsert = useCallback(
      ()=>{
        const newList = list.concat(parseInt(number))
        setNumber('')
        setList(newList)
      },[list, number]
  )
```

useCallback 함수의 첫번째 파라미터로는 생성 시키고 싶은 함수를 넣어주고, 두번째 파라미터로는 주시할 값을 배열로 넣어준다. 두번째 파라미터로 빈 배열을 전달 하면 컴포넌트가 처음 렌더링 될 때만 함수를 생성시킨다.

handleChange 같은 경우 참조하는 값이 없기 때문에 컴포넌트가 렌더링 될 때 단한번만 생성 하면 되지만 handleInsert 경우 list 와 number 값을 참조해야 하기 때문에 배열안에 list와 number 값을 넣어줘야 한다.

```js
useCallback(()=>{
  console.log('render')
},[])

useMemo(()=>{
  const fn = () => {
    console.log('render')
  }
  return fn
}, [])
```

위 코드는 같은 코드이다. **결론적으로 useCallback 은 useMemo 에서 함수를 반환하는 상황에서 더 편하게 사용 할 수 있는 Hook 이다. 문자열, 숫자, 객체 처럼 일반 값을 재사용하기 위해서는 useMemo, 함수를 재사용 하기 위해서는 useCallback 을 사용하면 된다. **

### 7. useRef

useRef 는 함수형 컴포넌트에서 ref 를 좀 더 편하게 사용하기 위해 사용한다.

```js
const inputRef = useRef(null)
...
const handleInsert = useCallback(()=>{
  const newList = list.concat(parseInt(number))
  setList(newList)
  setNumber('')
  inputRef.current.focus()
},[list, number])
...
<input value={number} onChange={handleChange} ref={inputRef}/>

```

useRef를 사용하면 객체가 생성되는데 이 객체안의 current 값이 해당 DOM 요소를 가리키게 된다.

### 7.1 로컬변수 만들기

```js
import React, {useRef} from 'react'

const RefSample = () => {
  const number = useRef(1)
  const setNumber = (n) => {
    number.current = n
  }
  const printNumber = () => {
    console.log(number.current)
  }
  return(
  	<div>Sample</div>
  )
}

export default RefSample
```

useRef를 사용하면 로컬변수를 만들 수 있다. **단, 여기서 number 값이 바뀌어도 컴포넌트는 리렌더링 되지 않는다. 따라서 렌더링이 필요하지 않고 값을 관리 할 때만 사용해야한다.**

### 8. 커스컴 Hook 만들기

여러 컴포넌트에서 비슷한 기능이 재사용 된다면, Hook 을 이용하여 로직을 재사용 할 수 있다.

### 8.1 usePromise

```js
// usePromise.js
import {useState, useEffect} from 'react'

export default function(promiseCreator, deps){
  const [ resolved, setResolved ] = useState(null)
  const [ loading, setLoading ] = useState( false)
  const [ error, setError ] = useState( null)

  const process = async () => {
    setLoading(true)
    try{
      const result = await promiseCreator()
      setResolved(result)
    }catch(err){
      setError(err)
    }
    setLoading(false)
  }

  useEffect(()=>{
    process()
  },deps)

  return [resolved, loading, error]
}
```

promiseCreator는 프로미스를 생성하는 함수이고 deps 는 useEffect hook의 두번째 파라미터로 전달 되는 배열이다. ( 빈 배열만 전달하면 컴포넌트가 Mount 될때만 실행 되겠지~)

주의 할점 useEffect 함수의 파라미터로 전달되는 함수에 async 사용하면 안된다. async 를 사용하면 함수가아닌 promise 를 반환하기 때문에 뒷정리 함수를 반환하지 못한다.

```js
import React from 'react';
import usePromise from "./usePromise"

function App() {
  const wait = () => {
    return new Promise( resolve => {
      setTimeout((()=>{resolve('hello hooks')}), 3000)
    })
  }
  const [ resolved, loading, error ] = usePromise(wait, [])
  if(loading) return <div>로딩중..</div>
  if(error) return <div> 에러 발생 {error}</div>
  if(!resolved) return null
  return(
      <div>{ resolved }</div>
  )
}

export default App;

```

위와 같이 사용하면 된다.

### 9. 다른 개발자 들의 Hooks

- <https://nikgraf.github.io/react-hooks/>
- <https://github.com/rehooks/awesome-react-hooks>
