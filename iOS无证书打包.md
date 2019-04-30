### iOS无证书打包ipa

- 使用模拟器运行realese版本app

- 运行成功后使用finder打开指定目录
  ```
  /Users/用户/Library/Developer/Xcode/DerivedData/应用名-dlhwiujwflbcfihdpspxiouwojjr/Build/Products/Release-iphonesimulator
  ```
- 使用里面的.app文件

- 创建一个名为“Payload”的文件夹 - >将“Product.app”放入“Payload” - >压缩“Payload” - >获取“Payload.zip” - >将名称更改为“Product.ipa”。

- sh脚本转换

  ```
  rm -rf appName
  mkdir appName
  mkdir appName/Payload
  cp -r appName.app appName/Payload/appName.app
  cp Icon.png appName/iTunesArtwork
  cd appName
  zip -r appName.ipa Payload iTunesArtwork

  exit 0
  ```
