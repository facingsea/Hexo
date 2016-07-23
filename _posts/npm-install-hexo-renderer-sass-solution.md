---
title: npm install hexo-renderer-sass失败问题
date: 2016-07-22 18:28:20
description: 解决npm install hexo-renderer-sass失败问题
categories: 
- npm
tags:
- npm
---

> 
以下方法在`Windows 7 64位` 及 `Windows 10 64位`上测试通过。 

今天尝试了`Hexo`主题 [maupassant-hexo](https://github.com/tufu9441/maupassant-hexo)， 但是在安装`hexo-renderer-sass`时，出了问题：
在执行以下命令后：
```bash
...
gyp verb architecture x64
gyp verb node dev dir C:\Users\wangzhf\.node-gyp\4.4.5
gyp verb found first Solution file build/binding.sln
gyp verb could not find "msbuild.exe" in PATH - finding location in registry
gyp verb "Release" dir needed to be created? null
gyp verb copying "node.lib" for x64 C:\Users\wangzhf\.node-gyp\4.4.5\Release\node.lib
gyp info spawn C:\Program Files (x86)\MSBuild\14.0\bin\msbuild.exe
gyp info spawn args [ 'build/binding.sln',
gyp info spawn args '/nologo',
gyp info spawn args '/p:Configuration=Release;Platform=x64' ]
...
gyp ERR! build error
gyp ERR! stack Error: \`C:\Program Files (x86)\MSBuild\14.0\bin\msbuild.exe\` failed with ex it code: 1
gyp ERR! stack     at ChildProcess.onExit (E:\code\Hexo\node_modules\hexo-renderer-sass\node_modules\node-sass\node_modules\node-gyp\lib\build.js:276:23)
gyp ERR! stack     at emitTwo (events.js:87:13)
gyp ERR! stack     at ChildProcess.emit (events.js:172:7)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:200:12)
gyp ERR! System Windows_NT 6.1.7600
gyp ERR! command "D:\\dev\\nodejs\\node.exe" "E:\\code\\Hexo\\node_modules\\hexo-renderer-sass\\node_modules\\node-sass\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild" "--verbose" "--libsass_ext=" "--libsass_cflags=" "--libsass_ldflags=" "--libsass_library="
gyp ERR! cwd E:\code\Hexo\node_modules\hexo-renderer-sass\node_modules\node-sass
gyp ERR! node -v v4.4.5
gyp ERR! node-gyp -v v3.4.0
gyp ERR! not ok
Build failed
npm ERR! Windows_NT 6.1.7600
npm ERR! argv "D:\\dev\\nodejs\\node.exe" "D:\\dev\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "install" "hexo-renderer-sass" "--save"
npm ERR! node v4.4.5
npm ERR! npm  v2.15.5
npm ERR! code ELIFECYCLE

npm ERR! node-sass@3.8.0 postinstall: \`node scripts/build.js\`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the node-sass@3.8.0 postinstall script 'node scripts/build.js'.
npm ERR! This is most likely a problem with the node-sass package,
npm ERR! not with npm itself.
npm ERR! Tell the author that this fails on your system:
npm ERR!     node scripts/build.js
npm ERR! You can get information on how to open an issue for this project with:
npm ERR!     npm bugs node-sass
npm ERR! Or if that isn't available, you can get their info via:
npm ERR!
npm ERR!     npm owner ls node-sass
npm ERR! There is likely additional logging output above.

npm ERR! Please include the following file with any support request:
npm ERR!     E:\code\Hexo\npm-debug.log
```
从以上日志信息中可以看出报错的是`node-gyp`，搜索后，在`node-gyp`的 [issue#119](https://github.com/nodejs/node-gyp/issues/119}) 看到应该是系统中缺少了C++的编译工具，查看`node-gyp`的 [安装条件](https://github.com/nodejs/node-gyp#installation) 中可以看出，需要安装`Visual C++ Build Tools`或者`Visual Studio 2015`，为了这个小东西，让我去安装微软的庞然大物，我表示还是难以接受的。
继续搜索，在`maupassant-hexo`的 [issue#29](https://github.com/tufu9441/maupassant-hexo/issues/29) 中发现可以先安装`node-sass`，安装方法见 [淘宝npm镜像](https://npm.taobao.org/mirrors) ，在最下面找到`node-sass`的安装命令，然后执行：

```bash
SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass npm install node-sass
```
安装成功后，然后再次执行安装`hexo-renderer-sass`的命令：
```bash
npm install hexo-renderer-sass --save
```
即可成功。
