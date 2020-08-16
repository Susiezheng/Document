# electron 构建跨平台的桌面应用程序

## Install

```bash

$ yarn add electron -save
$ yarn add electron-packager -save

```

## 配置命令项

```json

 "electron_dev": "yarn run build && electron electron.js",
 "electron_build": "electron-packager ./dist exeName --platform=win32 --arch=x64 --icon=./src/assets/img/logoNew.ico --overwrite"

```

## 根目录新建 electron.js

```js
// Modules to control application life and create native browser window
const { app, BrowserWindow } = require('electron');
const path = require('path');

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let mainWindow;

function createWindow() {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      webSecurity: false,
      nodeIntegration: true,
      // preload: path.join(__dirname, 'preload.js')
    },
  });

  // and load the index.html of the app.
  // mainWindow.loadFile('index.html')
  // mainWindow.loadFile(`${__dirname}\\dist\\index.html`)
  //请求的后端地址
  mainWindow.loadURL('http://XXXX.com/');

  // Emitted when the window is closed.
  mainWindow.on('closed', function () {
    mainWindow = null;
  });
}

app.on('ready', createWindow);

// Quit when all windows are closed.
app.on('window-all-closed', function () {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') app.quit();
});

app.on('activate', function () {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (mainWindow === null) createWindow();
});
```

## 配置图片等静态文件

```js
  //  生成环境：打包后生成的代码中，静态文件的查找路径，默认为'/'
    publicPath: process.env.NODE_ENV === 'production'
        ? './static/'
        : '/',
    // 当运行 build 时静态文件的存放路径
    outputDir: path.resolve(__dirname, '../static'),
    // 放置生成`的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录
    assetsDir: './',
    // 指定生成的 index.html 的输出路径 (相对于 outputDir)
    indexPath: path.resolve(__dirname, '../index.html'),
    publicPath: './',
    outputDir: 'dist',
    assetsDir: 'static',
```
