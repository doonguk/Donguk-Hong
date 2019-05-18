# Git

- Commit

  ```bash
  git commit
  ```

  1. Git 저장소에 나의 디렉토리에 있는 모든 파일에 대한 스냅샷을 기록하는 것
  2. 커밋할 때마다 디렉토리 전체를 복사하는게 아니라 이전 버전과 다음버전의 변경내역( delta )을 저장한다.
  3. 대부분의 커밋이 자신의 부모 커밋을 가리킨다.

- Branch

  > 브랜치는 서둘러서, 그리고 자주 만들자!

  

  ```bash
  $ git branch [branch-name] //create branch
  ```

  1. Branch는 특정 commit에 대한 참조(reference)다.

  2. 하나의 커밋과, 그 부모 커밋들을 포함하는 작업 내역 느낌

  

  ```bash
  $ git checkout [branch-name] //해당 branch로 이동
  ```

  1. Branch로 이동후 커밋하자 그래야 해당 branch에 작업내역이 추가되지

  

-  Merge

  ```bash
  $ git merge
  ```

  1. 두 별도의 브랜치를 합치는 방법
  2. 두 개의 부모를 가리키는 특별한 커밋을 만들어낸다. ( 한 부모의 모든 작업내역과 나머지 부모의 모든 작업, 그리고 그 두 부모의 모든 부모들의 작업내역을 포함한다. )
  
  <img width="323" alt="스크린샷 2019-05-11 오후 8 06 03" src="https://user-images.githubusercontent.com/39187116/57568922-c1c5f280-7428-11e9-8f0e-7186ee40124c.png">

  ```bash
  //example
  // master branch에서 시작
  $ git branch bugFix
  $ git checkout bugFix
  $ git commit
  $ git checkout master
  $ git commit
  $ git merge bugFix
  ```



- Rebase

  ```bash
  $ git rebase [branch-name]
  ```

  1. 두 별도의 브랜치를 합치는 두번째 방법
  2. 커밋들을 모아서 복사한 뒤, 다른 곳에 떨궈 놓는 것
  3. 커밋들을 보기좋게 한 줄로 만들 수 있다
  4. <code>[branch-name]</code> 브렌치 밑으로 현재 헤드가 위치한 브랜치의 커밋들을 떨궈 놓는다

<img width="326" alt="스크린샷 2019-05-11 오후 7 58 58" src="https://user-images.githubusercontent.com/39187116/57568920-c12d5c00-7428-11e9-8a3a-37ce7756559f.png">

```bash
$ git rebase master
$ git checkout master
$ git rebase bugFix
```

<img width="329" alt="스크린샷 2019-05-11 오후 8 00 59" src="https://user-images.githubusercontent.com/39187116/57568921-c12d5c00-7428-11e9-8e22-e1ae18310b35.png">

master가 bugFix 브랜치의 부모쪽에 있었기 때문에, 단순히 그 브랜치를 더 앞쪽의 커밋을 가리키게 한다.
