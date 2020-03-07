# quasar创建electron应用失败

[因为网络问题, 安装不了electron](https://github.com/quasarframework/quasar/issues/5892)

```shell
npm install -g cross-env
cross-env npm_config_electron_mirror="https://npm.taobao.org/mirrors/electron/" npm_config_electron_custom_dir="7.1.7" npm install electron@7.1.7
```


```shell
qingyan@thinkpad:/app/workspaces/quasar/hello$ quasar dev -m electron --verbose

 Dev mode.......... electron
 Pkg quasar........ v1.7.4
 Pkg @quasar/app... v1.5.2
 Debugging......... enabled

 app:quasar-conf Reading quasar.conf.js +0ms
 app:dev Checking listening address availability (0.0.0.0:8080)... +3ms
 app:webpack Extending Renderer process Webpack config +245ms
 app:webpack Extending Main process Webpack config +118ms
 app:generator Generating Webpack entry point +3ms
 app:dev-server Booting up... +1ms


 Renderer process █████████████████████████ [100%] in ~5s
 Main process                               [0%]



 DONE  Compiled successfully in 5403ms                                                                                                                                                                    4:42:27 PM

ℹ ｢wds｣: Project is running at http://0.0.0.0:8080/
ℹ ｢wds｣: webpack output is served from 
ℹ ｢wds｣: 404s will fallback to /index.html
 app:electron Building main Electron process... +6s

 Renderer process █████████████████████████ [100%] in ~5s
 Main process     █████████████████████████ [100%] in ~528ms

 app:electron Webpack built Electron main process +530ms

Hash: e7214d6829f6e5d46647
Version: webpack 4.41.5
Time: 530ms
Built at: 01/19/2020 4:42:28 PM
           Asset     Size         Chunks             Chunk Names
electron-main.js  220 KiB  electron-main  [emitted]  electron-main
Entrypoint electron-main = electron-main.js

WARNING in ./node_modules/electron-debug/index.js 96:45-58
Critical dependency: the request of a dependency is an expression
 @ ./src-electron/main-process/electron-main.dev.js
 @ multi ./src-electron/main-process/electron-main.dev.js

WARNING in ./node_modules/electron-debug/index.js 97:61-74
Critical dependency: the request of a dependency is an expression
 @ ./src-electron/main-process/electron-main.dev.js
 @ multi ./src-electron/main-process/electron-main.dev.js

 app:electron Booting up Electron process... +2ms
 app:spawn ⚠️  Command name was not available. Please run again. +1ms
```
