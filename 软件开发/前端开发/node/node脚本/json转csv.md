```
var fs = require('fs')
var { Parser } = require('json2csv')

var jsonData=[]
var ppser=new Parser()
for (let num = 1; num <= 1; num ++) {
  // let src = './tangshi/poet.tang.' +num + '.json'
  let src = './author/authors.tang.json'
  temp=JSON.parse(fs.readFileSync(src))
  jsonData=jsonData.concat(temp)
}
jsonData=ppser.parse(jsonData)
// const address='./res/tangshi.csv'
const address='./res/authorts.csv'
fs.writeFile(address, jsonData, function (err) {
  if (!err) {
    console.log('yes')
  }
})

```

