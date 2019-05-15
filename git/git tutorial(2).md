# Git

- #### HEAD

1. HEAD란 현재 체크아웃된 커밋을 가리킨다. ( 현재 작업중인 커밋을 말한다.)

2. HEAD는 항상 작업트리의 가장 최근 커밋을 가리킨다. 작업트리에 변화를 주는 git 명령어들은 대부분 HEAD를 변경하는 것으로 시작한다.
3. 일반적으로 브랜치 이름을 가리키고 있다.

- #### HEAD 분리하기

HEAD를 분리한다는 것은 HEAD를 브랜치 대신에 커밋에 붙이는 것을 의미한다.

```bash
// before : HEAD -> master -> C1(commit 해시값) 상황에서
$ git checkout C1
// after : HEAD -> C1
```

commit 해시 값 확인 명령어 <code>git log</code>



- #### 상대 참조

상대 참조로 어떤 기억할 만한 지점에서 출발해서 이동하여 다른 지점에 도달해 작업을 할 수 있다.

상대 커밋은 두가지 간단한 방법이 있다.

1. 한번에 한 커밋 위로 움직이는 <code>^</code>

2. 한번에 여러 커밋 위로 올라가는 <code>~<num></code>

먼저 캐럿 ( ^ ) 연산자 부터 알아보면, 참조 이름에 하나씩 추가할 때마다, 명시한 커밋의 부모를 찾아가게 된다.

```bash
$ git checkout master^
// 캐럿 연산자를 여러번 사용도 가능하다. ex) master^^^^^ 
```

![깃1](https://user-images.githubusercontent.com/39187116/57782803-e3481680-7767-11e9-86ad-43694e790af4.PNG)

위와 같이 master의 부모를 HEAD가 가리키게 된다.

다음으로 틸드 ( ~ ) 연산자 이다. 틸드 연산자는 올라가고 싶은 부모의 갯수가 뒤에 숫자로 온다.

```bash
$ git checkout HEAD~4
```

커맨드라인 입력전 HEAD가 위치한 커밋의 4번 위 부모로 HEAD를 옮긴다.



- #### 브랜치 강제로 옮기기( 상대 참조 활용)

브랜치를 강제로 옮기는 방법은 <code>-f</code> 옵션을 이용한다.

```bash
$ git branch -f master HEAD~3
```

위 커맨드라인은 (강제로) master branch를 HEAD에서 세번 뒤로 옮긴 것 이다.



- #### Git 에서 작업 되돌리기

Git에서 변경한 내용을 되돌리는 방법은 크게 두가지가 있다

1. <code>git reset</code> <돌아가고 싶은 커밋>

2. <code>git revert</code> <되돌릴 커밋>

<code>git reset</code> 은 브랜치로 하여금 예전의 커밋을 가리키도록 이동시키는 방식으로 변경 내용을 되돌린다. 마치 애초에 커밋하지 않은 것처럼 예전 커밋으로 브랜치를 옮기는 것이다. 이것을 "히스토리를 고쳐쓴다" 라고도 한다.

```bash
$ git reset HEAD^
```

![깃2](https://user-images.githubusercontent.com/39187116/57783079-72552e80-7768-11e9-9cfb-7443494a59d8.PNG)


reset 커맨드 결과 C2 해시값을 가지는 커밋이 애초에 없었던 것처럼 사라지고 이전 커밋을 HEAD가 가리키게 된다.

하지만.. 각자의 컴퓨터에서 작업하는 로컬 브랜치의 경우 <code>reset</code> 을 잘 쓸 수 있겠지만,  "히스토리를 고쳐쓴다" 라는 점 때문에 다른 사람이 작업하는 리모트 브랜치에는 쓸 수 없다.

변경분을 되돌리고, 이 되돌린 내용을 **다른 사람들과 공유하기 위해서는 <code>git revert</code> 를 사용한다**

![깃3](https://user-images.githubusercontent.com/39187116/57782811-e4794380-7767-11e9-91de-b2773352220a.PNG)

이러한 커밋 트리에서

```bash
$ git revert HEAD
```

커맨드를 입력할 경우

![깃4](https://user-images.githubusercontent.com/39187116/57782813-e511da00-7767-11e9-9dff-0d3062db148e.PNG)

아래에 새로운 커밋이 생긴다.  이 새로운 커밋에 변경내역이 기록되는데, 이 변경내역은 정확히 C2 커밋 내용의 반대되는 내용이다. <code>revert</code> 를 하면 다른 사람들에게도 변경내역을 PUSH 할 수 있다.
