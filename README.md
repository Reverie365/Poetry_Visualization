<h1 align="center">Poetry Visualization</h1>

> 在线体验链接：https://2aurora2.github.io/Poetry_Visualization/#/

## 飞花令大模型代理服务运行方式

本项目使用的大模型是Doubao-1.5-pro-32k，具体调用方式可参考[官方文档](https://www.volcengine.com/docs/82379/1298454)，但是使用发现在前端项目中直接请求该大模型API接口会出现跨域问题，同时考虑到后续使用Github Pages部署当前纯前端项目，我们采用下述方案解决：

**(1) 搭建代理服务器**

我们使用 Node.js 和 Express 搭建了一个轻量级后端服务器。该服务器在前端与大模型 API 之间充当代理，通过配置 CORS 中间件允许来自指定前端域名的请求，并对大模型 API 进行封装和转发，从而有效解决了跨域限制，确保前后端的顺畅交互和项目的顺利部署。

代理服务端具体代码存在于文件夹`proxy`中，其中`.env.example`文件存储着大模型API相关的一些环境变量，具体获取方式文件中有说明，获取后将文件重命名为`.env`，然后终端运行`node index.js`即可本地运行该代理服务器。

```cmd
cd proxy
npm install
node index.js
```

如果仅打算本地使用该代理服务，只需在根目录下的`.env`文件（将`.env.example`更名而来）中填入对应的后端服务器接口`http://localhost/chat`到`VITE_CHAT_URL`参数即可。但是本项目需要使用Github Pages部署以便公网访问，但没有考虑用其他成本来租个服务器跑代理服务端（doge，因此利用**微信云托管**来部署该代理服务。

**(2) 微信云托管部署代理服务**

具体部署方式可参考[快速开始 / 自定义部署 / Nodejs](https://developers.weixin.qq.com/miniprogram/dev/wxcloudservice/wxcloudrun/src/quickstart/custom/node.html)，通过这种方式就可以在前端项目中直接请求云托管得到的公网域名，得到大模型的返回结果，并将得到的公网域名`https://xxxxxxxx`+`/chat`填入根目录下的`.env`文件（将`.env.example`更名而来）中的`VITE_CHAT_URL`参数即可，这样项目也无需其他成本来部署代理服务。

**(3) 白嫖免费阿里云服务器部署代理服务**

好吧，微信云托管不太稳定()，还是用阿里云服务器吧，配置文档在aliyun.docs中，本地部署后记得修改`.env`文件中的`VITE_CHAT_URL`参数。

## 本地前端项目运行

前文说明了代理服务的运行方式，有了代理服务之后，就可以正常运行该前端项目了（记得先把`.env`文件中的参数给填了），运行的终端命令如下（在根目录下运行）：

```cmd
npm install
npm run dev
```

## npm run build 出现 JavaScript heap out of memory解决方法

* Linux: export NODE_OPTIONS="--max-old-space-size=4096"
* Windows: set NODE_OPTIONS=--max-old-space-size=4096

> Nginx部署项目参考：[https://blog.51cto.com/AmbitionGarden/14111626](https://blog.51cto.com/AmbitionGarden/14111626)