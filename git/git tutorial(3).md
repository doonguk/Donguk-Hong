# Git



- #### 작업을 여기저기 옮기기 ( 이 일은 여기에 저 일은 저기에 )

```bash
$ git cherry-pick <commit> <commit2> <...>
```

`git cherry-pick` 명렁어는 현재 위치(`HEAD`) 아래에 있는 일련의 커밋들에대한 복사본을 만들겠다는 것을 간단히 줄인 말이다. ( 현재 헤드가 위치한 커밋 밑에 `<commit>` `<commit2> 의 복사본을 떨궈준다.`)

<img width="311" alt="스크린샷 2019-05-18 오후 1 21 53" src="https://user-images.githubusercontent.com/39187116/57964606-ce8b9e80-7972-11e9-8470-4cfe16f13ff5.png">

위와 같은 repository가 있다고 가정 할 때

```bash
$ git cherry-pick C2 C4 
```

를 한다면?

<img width="322" alt="스크린샷 2019-05-18 오후 1 23 34" src="https://user-images.githubusercontent.com/39187116/57964609-d0556200-7972-11e9-8ff4-e153f1a0c292.png">

C2와 C4 커밋의 복사본을 현재 헤드가 위치했던 커밋 밑에 떨궈준다!



- #### Git Interactive Rebase

`git cherry pick` 명령어는 우리가 원하는 커밋이 무엇인지 알고 있을 때 아주 유용하다. 근데 원하는 커밋을 모르는 상황에는..? `Interactive Rebase` 를 사용하자!

`interactive Rebase` 가 의미하는 뜻은 `rebase` 명령어에 `-i` 옵션을 같이 사용한다는 것이다. 이 옵션을 추가하면, `git` 은 리베이스의 목적지가 되는 곳 아래에 복사될 커밋들을 보여주는 UI를 띄운다. 각 커밋을 구분할 수 있는 각각의 해시들과 메시지도 보여준다. ( _그러나 실제 `git` 에서는 UI 창을 띄우는 대신에 `vim` 과 같은 텍스트 편집기에서 파일을 연다.._)

<img width="311" alt="스크린샷 2019-05-18 오후 1 37 07" src="https://user-images.githubusercontent.com/39187116/57964610-d0edf880-7972-11e9-82b5-b62832d21fb5.png">

위와 같은 repository가 있다고 가정할 때 아래 커맨드를 입력하면

```bash
$ git rebase -i HEAD~4
```

<img width="649" alt="스크린샷 2019-05-18 오후 1 37 27" src="https://user-images.githubusercontent.com/39187116/57964611-d4817f80-7972-11e9-821d-458438c3681d.png">

위와 같은 UI가 뜬다. ( 실제 `git`에서는 다르겠지만,,) 여기에서 원하는 커밋을 선택해서 순서를 바꾸거나 뺄 수 도있다.

<img width="301" alt="스크린샷 2019-05-18 오후 1 38 28" src="https://user-images.githubusercontent.com/39187116/57964612-d5b2ac80-7972-11e9-9b5b-cb84f0ed0107.png">

C4 커밋을 빼고 순서를 바꿨다. HEAD는 바뀐 커밋을 가리키게 된다.
