## React Higher-Order-Component ( HOC )

코딩을 할 때 자주 반복해서 작성하게 되는 로직은 함수를 이용하여 코딩을 한다. 

리액트 컴포넌트에서도 UI 가 재사용된다고 생각되면 컴포넌트를 만들어서 재사용을 한다.

그런데 컴포넌트 기능 상에서도, 자주 반복되는 코드들이 나타날 수 있다. 이것을 HOC를 이용하여 해결 할 수 있다. HOC는 하나의 함수이다. 함수를 이용하여 컴포넌트에 자신이 만든 특정 기능을 부여하는 것 이다.



- #### 반복되는 코드 발견하기

```js
//Post.js
import React, {useState, useEffect} from 'react'
import axios from 'axios'

const Post = () => {
	const [data, setData] = useState('')
	async initialize(){
    try{
      const { data } = await axios.get('https://jsonplaceholder.typicode.com/posts/1')
      setData(data)
    }catch(e){
      console.error(e)
    }
    useEffect(()=>{
      this.initialize()
    },[])
  }
  if(!data) return null
  return(
  	<div>
    {JSON.stringify(data)}
    </div>
  )
}

export default Post
```

이 컴포넌트는 특정주소에 get 요청을 날리고 Hooks state에 데이터를 담아 JSON 형태로 렌더링 해주는 것이다.

```js
// Comment.js
import React, { useState, useEffect } from 'react'
import axios from 'axios'

const Comment = () =>{
  const [data, setData] = useState('')
  async initialize(){
    try{
      const { data } = await axios.get('https://jsonplaceholder.typicode.com/comments?postId=1')
      setData(data)
    }catch(e){
      console.error(e)
    }
  }
  useEffect(()=>{
    this.initialize()
  },[])
  
  if(!data) return null
  return(
  	<div>{JSON>stringify(data)}</div>
  )
}

export default Comment
```

위의 Post 컴포넌트와 요청하는 url만 다른 Comment 컴포넌트 이다.



- #### HOC 작성하기

위의 반복되는 코드를 없애기 위해서 하나의 함수(HOC)를 작성한다. 

HOC 를 네이밍할 때는 <code>with___</code> 형식으로 한다. 위 예제에서는 웹요청을 하는 HOC를 만들기 위해 withRequest로 네이밍을 한다. 

HOC의 원리는 파라미터로 컴포넌트( Presentational Component )를 받아오고, 함수 내부에서 새 컴포넌트를 만든 다음에 해당 컴포넌트 내부에서 파라미터로 받은 컴포넌트를 렌더링 하는 방식이다. 함수 내부에서 만든 새 컴포넌트는 자신이 받은 props에 추가 props( 예제에서는 웹요청 결과물 ) 를 그대로 파라미터로 받은 컴포넌트에 넣어준다.

```js
//withRequest.js

import React, { Component } from 'react'
import axios from 'axios'

const withRequest = (url) => (WrappedComponent) => {
  return class extends Component{
    state = {
      data : null
    }
  	async initialize(){
      try{
        const { data } = await axios.get(url)
        this.setState({
          data
        })
      }catch(e){
        console.error(e)
      }
    }
  	componentDidMount(){
      this.initialize()
    }
    render(){
      const { data } = this.state
      return(
      	<WrappedComponent {..this.props} data={data} />
      )
    }
  }
}
export default withRequest
```

웹 요청결과 받은 data 를 파라미터로 받은 컴포넌트에 props로 전달해 주었다.



- #### HOC 사용하기

  ```js
  //Post.js
  import React from 'react'
  import withRequest from './withRequest'
  
  const Post = ({data}) => {
    if(!data) return null
    return(
    	<div>
      {JSON.stringify(data)}
      </div>
    )
  }
  
  export default withRequest('https://jsonplaceholder.typicode.com/posts/1')(Post)
  ```

  와 같이 HOC 를 사용하여 내보내면 된다.

  또는,

  ```js
  const PostWithData = withRequest('https://jsonplaceholder.typicode.com/posts/1')(Post)
  export default PostWithData
  ```



- #### HOC 확장

[recompose](https://github.com/acdlite/recompose) - 페이스북 개발자가 만든 HOC 컬렉션 라이브러리

