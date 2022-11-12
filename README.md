# mono-repo-test-with-npm-workspaces

1. 사용할 모듈을 workspaces에 등록.
handler에서 utils를 가져다 쓸 것이므로, handler도 workspaces에 포함이 되어야함.

```json
// {$root}/package.json
{
  "name": "mono-repo-test",
  "version": "1.0.0",
  "description": "mono-repo-test",
  "workspaces": [
    "packages/utils",
    "packages/handler"
  ]
}
```

- handler가 workspaces에 포함되지 않은 경우? <br/>
handler에서 utils에 접근한 경우, npm에서 찾지 못한다고 에러가 발생함. (npm ERR! 404 Not Found - GET https://registry.npmjs.org/@mono-repo-test%2futils - Not found)<br/>
따라서 모듈을 찾을 때는 '1) 같은 workspaces에서 찾음 -> 2) npm에서 찾음'의 과정이 이뤄진다는 것을 알 수 있음.


```js
// handler/index.js
const add = require('@mono-repo-test/utils')
```


2. handler쪽에서 utils의 정확한 버전을 명시해야함.

```json
{
  "name": "handler",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "dependencies": {
    "@mono-repo-test/utils": "1.0.0"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

- 정확한 버전을 명시하지 않은 경우?<br/>
npm i로 설치가 되지 않음.

- 설치 이후에 버전을 바꾼 경우?<br>
이 경우는 상관 없음.

3. 설치 이후 utils의 코드를 변경하는 경우?<br/>
유틸 파일을 링크만 해주는 듯, 유틸코드가 변경되면 그 즉시 {$root}/node_modules 내의 코드가 함께 변경됨. (즉시 반영이 된다는 말.)


4. 유틸 모듈의 이름에 패키지 이름이 포함이 되어야 하는가?

- 꼭 아래와 같은 형식이 되어야 하는가?<br/>

```json
{
  "name": "@mono-repo-test/utils",
  "version": "1.0.0",
  "description": "",
  "main": "add.js",
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```

```js
// handler/index.js
const add = require('@mono-repo-test/utils')
```
그렇지 않음. 편의를 위해서 @mono-repo-test/utils와 같이 사용할 뿐, '1) 같은 workspaces에서 찾음 -> 2) npm에서 찾음'의 과정을 통해 모듈을 찾게 되므로
같은 워크스페이스에만 추가해주면 됨.

- 그럼 왜 @mono-repo-test/utils와 같이 사용하는가?<br/>
  npm을 통해 호출하는 일반 패키지와 이름이 비슷할 수 있기 때문에 그런 것 같음.

<br/>

5. 개별 패키지 디렉토리에서 npm i을 통해서 모듈을 설치하는 경우 최상위 폴더 node_modules만 생성됨. (개별 디렉토리에는 안생김.)

6. package.json 내 main은 어떤 역할을 하는가?

```json
{
  "name": "@mono-repo-test/utils",
  "version": "1.0.0",
  "description": "",
  "main": "add.js",
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```
해당 모듈을 import 하는 경우 바라보는 파일임. 

- adds.js로 바꾸는 경우?<br/>

  에러 발생. Cannot find module '/node_modules/@mono-repo-test/utils/adds.js'. Please verify that the package.json has a valid "main" entry
  정확한 엔트리 경로를 입력해달라고 함.

