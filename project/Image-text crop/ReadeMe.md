# 이미지 crop ( text 추출 )

> 사용한 기술 스택

AWS s3, request 모듈, image-size 모듈, Google Vision API, Jimp 모듈



#### AWS s3

_사실 필요하지 않았다 이유는 애초에 s3의 object url 을 받은 뒤 작업을 시작하기 때문에..그래도 처음이니까!_

> 준비 작업 ( S3 버킷 생성 및 사용해보기 !)

1. Access Key, Security Key 발급 받기

AWS 홈페이지에 들어가서 상단에 닉네임 클릭후 '내 보안자격 증명' 에 들어간다. 그럼 IAM 계정을 만들어서 보안 권한을 제한 하라고 뜨는데 일단 처음이니까 IAM 계정 없이 진행 하였다. 그후 Access Key, Security Key 다운로드

2. S3 버킷 생성

3. AWS sdk 설치 

<code>npm i aws-sdk</code>

4. 프로젝트내 awscofig 파일 작성

   ```json
   {
     "accessKeyId" : "aceessKey 입력",
     "secretAccessKey" : "secretKey 입력",
      "region": "ap-northeast-2"
   }
   ```

나는 프로젝트 루트 디렉토리에 config 폴더를 만들고 그 안에 json 파일로 작성했다. region 관련 정보는 [여기](<https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions>) 를 참고하였다.

5. Aws sdk 로딩

```javascript
let AWS = require("aws-sdk");
AWS.config.loadFromPath(__dirname + "/../config/awsconfig.json");
let s3 = new AWS.S3();
```

<code>AWS.config.loadFromPath() </code> 메소드를 활용하여 AWS sdk 환경설정을 해준다.

6. Create a request param

AWS bucket 에 전달할 파라미터 obj 를 만들어준다. 

```javascript
const getParams = {
    Bucket: 'image-crop', //my bucket name
    Key: 'data/target.png' // path
}
```

Image-crop 이름을 가진 bucket 에서 data폴더안의 target.png 를 가져오는 param 이다.

7. Get Object

```javascript
s3.getObject(getParams, (err, data) => {
  if(err) console.log(err)
  else{
    console.log(data)
  }
})
```

만든 파라미터를 s3 에 전달하면 data로 결과값이 오는데, 나 같은 경우는 이미지를 요청 했기 때문에 이미지 버퍼값과 관련 데이터 들이 response 된다.

<br/>

#### request 모듈

> url 로 이미지 접근하기

내가 해야하는 작업은 url 로 받아서 이미지를 받은뒤 가공해야한다.

퍼블릭에서 버킷 안의 Object에 접근하려면 퍼블릭 권한 설정을 해야한다. S3 콘솔에서 해당 버킷의 권한 탭에 들어가면 설정 가능하다.

이미지를 url 을 통해서 받기 위해 request 모듈을 사용 하였다.

```js
const request = require('request')
const fs = require('fs');

const requestOptions  = { method: "GET"
  ,url : '이미지의 s3 object url',                        
  ,encoding: null
};


request(requestOptions).pipe(fs.createWriteStream('./asset/original/target.png'))
```

const request = require('request') 에서 request 상수는 http request 를 발생시켜주는 상수이다.

request 요청에 pipe 설정으로  stream 을 생성하여 ' ' 경로에 사진을 저장한다.



#### image-size 모듈

> image의 width, height 구하기

url 을 통해 저장한 이미지의 width 값 height 값을 구하기위해 사용하였다. (crop 하기위해서는 이미지의 width, height 값을 알아야 하기 때문에)

<code>npm i image-size --save</code>

```JavaScript
const { promisify } = require('util')
const sizeOf = promisify(require('image-size'))

module.exports = async() => {
  try{
		return const dimentions = await sizeOf(imagePath)
  }catch(err){
    console.log(err)
  }
}
```



async , await 을 사용하기 위해서 util 모듈의 promisify 함수를 사용하였다. promisify 함수는 콜백 패턴을 프로미스 패턴으로 바꿔주는 유용한 함수다.

위와 같이 sizeOf 함수를 통해 얻은 dimensions 값으로 dimensions.width, dimentions.height 을 통해 이미지의 width, height 을 구할수 있다.



#### Google Vision API

> 시작하기

1. Goole Cloud Platform Console에 들어가면 하단에 API 사용 설정이 있다. 그곳에 들어간뒤 'vision api' 검색 후 사용 설정을 해준다.

2. 구글 API 를 사용하기 위한 Access Key 를 다운 받는다. 자세한 사항은 [여기](<https://cloud.google.com/vision/docs/quickstart-client-libraries>) 를 참고 하였다.

3. 받은 Access Key 를 OS 환경변수에 세팅해준다.

   ```bash
   export GOOGLE_APPLICATION_CREDENTIALS="/home/user/Downloads/service-account-file.json"
   ```

4. 클라이언트 라이브러리를 설치한다.

   <code>npm install --save @google-cloud/vision</code>

   여기까지 하면 환경설정은 끝난다.

> 이미지안 TEXT 값 추출하기

```js
const vision = require('@google-cloud/vision');


// Creates a client
const client = new vision.ImageAnnotatorClient();

async function visionAPI(height, file) {
  const topCutPoint = Math.round( height / 100 ) * 20
  const bottomCutPoint = Math.round( height /100) * 85 //밑에서 15퍼센트
  console.log('cutPoint', topCutPoint, bottomCutPoint)
  try{
    let positions = []
    let location = {}
    let forCount = 0
    const [textResult] = await client.documentTextDetection(file)
    const fullTextAnnotation = textResult.fullTextAnnotation
    console.log(`Full text:\n ${fullTextAnnotation.text}`);
    fullTextAnnotation.pages.forEach( page => {
      page.blocks.forEach(block =>{
        console.log('-------Block------\n')
        let wordTexts = ''
        const pLength = block.paragraphs.length
        block.paragraphs.forEach((para, index)=>{
          para.words.forEach( word =>{
            const wordText = word.symbols.map(s => s.text).join('')
            wordTexts = wordTexts + wordText
            if(word.boundingBox.vertices[1]['y'] > bottomCutPoint){
              positions.push(word.boundingBox.vertices[1]['y'])
            }
            positions.push(word.boundingBox.vertices[2]['y']) // 글자 오른쪽 밑 y값
          })
        })
        console.log('position', positions)
        console.log('text :', wordTexts)
      })
    })
    positions.forEach( (position, index) =>{
      if(positions[index] < topCutPoint && positions[index+1] > topCutPoint ){
        console.log('top : ', position)
        location.top = position
      }
      if(positions[index] > bottomCutPoint && forCount === 0 ){
        console.log('bottom :', position)
        location.bottom = position
        forCount = 1
      }
    })
    return location
  }catch(err){
    console.log("ERROR :", err)
  }
}

module.exports = async(height, fileName) => {
  const file = `${__dirname}/../asset/original/${fileName}.jpeg`
  return await visionAPI(height,file)
}
```

<code>client.documentTextDetection(filename)</code> 메소드를 통해 이미지 안 텍스트 값을 추출 할 수 있다. 그 밑에 코드들은 block 단위로 text 값과 block 가장 왼쪽 글자의 좌표, 가장 오른쪽 글자의 좌표 를 console에 찍은 코드이다. 이렇게 얻은 좌표를 이용하여 이미지를 crop 할 계획이다.

##### Jimp module

> image crop 후 저장하기

<code>npm i jimp</code>

JImp 는 javascript 이미지 관련 처리를 쉽게 하게 도와주는 모듈이다, 기본적으로 resize 함수, 퀄리티 설정, greyscale 등을 기본적으로 제공한다. Crop 을 하기 위해서는 crop() 함수를 이용하면 된다.

```javascript
const Jimp = require('jimp')

async function crop(location, image, targetPath, cropPath, fileName){
  if(location.top) {
    const file = await Jimp.read(targetPath)
    file.crop(0,0, image.width, location.top)
        .write(cropPath + `top-${fileName}.jpeg`)
  }
  if(location.bottom){
    const file = await Jimp.read(targetPath)
    file.crop(0, location.bottom, image.width, image.height - location.bottom  )
        .write(cropPath +`bottom-${fileName}.jpeg`)
  }
  const file = await Jimp.read(targetPath)
  file.crop(0,location.top, image.width, image.height - location.top-(image.height-location.bottom) )
      .write(cropPath +`crop-${fileName}.jpeg`)
}

module.exports = async(location, image, fileName) => {
  const targetPath = `${__dirname}/../asset/original/${fileName}.jpeg`
  const cropPath = `${__dirname}/../asset/asset-crop/`
  await crop(location, image, targetPath, cropPath, fileName)
}
```

crop 함수의 파라미터는 (x, y, width, height) 값이 오는데 이미지의 맨왼쪽위 모서리가 x,y 의 (0,0)에 해당하고 지정한 width와 height 값 만큼 crop 하는 함수다. 나는 글자가 상단에 있을경우와 하단에 있을경우를 나눠서 3등분을 하는 코드를 작성하였다.(메인사진과 글자부분 사진을 나눠서 저장하기 위해서)

##### 알고 있어야 하는 부분

Request, Image-Size, vision, Jimp 모듈을 index.js 에서 불러오는 과정에서 문제가 있었다. request 모듈에서 url 을 이용하여 이미지를 서버에 저장을 하고 image-size, vision 등 나머지 모듈이 실행 되어야하는데 request module 이 다른 모듈과 함께 동기적으로 작동하게 하기위해서는 

```js
//request 수정
var picStream = fs.createWriteStream('doodle.png');
picStream.on('close', function() {
  //나머지 모듈을 넣는다
});
request('s2 object url').pipe(picStream); 
```

이와 같이 writeStream 이벤트 헨들러에 'close' 이벤트를 바인딩 해줌으로써 동기적으로 처리 할 수 있었다.
