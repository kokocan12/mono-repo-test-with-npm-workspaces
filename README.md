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


4. 유틸 모듈의 이름에 패키지 이름이 포함이 되어야함.

아래와 같은 형식이 되어야함.

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

그래야 다음과 같이 호출할 수 있음.

```js
// handler/index.js
const add = require('@mono-repo-test/utils')
```

- @mono-repo-test/utils이 아닌 utils로 바꾸고 const add = require('utils')로 사용한다면?<br/>
  npm i로 설치가 안되고, 해당 패키지를 찾을 수 없다는 오류가 발생함. 꼭 지켜야 하는 네이밍 규칙인듯.




