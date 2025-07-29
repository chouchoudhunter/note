## 1.在项目根目录下新建`.npmrc`文件，然后加入`store-dir=./.pnpm-store`意思是把pnpm依赖的缓存存在项目根目录下
## 2.使用`npm pcak pnpm `打包pnpm,使用`npm install -g ./pnpm-x.y.z.tgz`离线安装
## 3.有的项目会制定pnpm版本，在`package.json`中的`packageManager`属性,这时我们上一步安装的pnpm版本可能无法使用。要么删掉这一行，重新进行`pnpm install`。要么去`C:\Users\用户\AppData\Local\pnpm\.tools\pnpm`目录下把下载的项目指定版本移动到离线环境同等目录里。