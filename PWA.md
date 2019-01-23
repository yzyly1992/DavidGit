### PWA -- 网站秒变应用程序

Progressive Web App (PWA) is a term used to denote a new software development methodology. Unlike traditional applications, progressive web apps are a hybrid of regular web pages (or websites) and a mobile application.   

Progressive Web App 渐进式网页应用程序，一个新的前端技术。不但可以加速载入网页，还能添加应用到移动端主屏幕 Andriod/IOS，并进行“推送通知”。  



官网上给出 PWA 的宣传是 ： Reliable（ 可靠的 ）、Fast（ 快速的 ）、Engaging（ 可参与的 ） 



[Reliable](https://developers.google.com/web/progressive-web-apps/#reliable) : Load instantly and never show the downasaur, even in uncertain network conditions.

[Fast](https://developers.google.com/web/progressive-web-apps/#fast) : Respond quickly to user interactions with silky smooth animations and no janky scrolling.

[Engaging](https://developers.google.com/web/progressive-web-apps/#engaging) : Feel like a natural app on the device, with an immersive user experience.  



Reliable ：当用户从手机主屏幕启动时，不用考虑网络的状态是如何，都可以立刻加载出 PWA。

Fast：这一点应该都很熟悉了吧，站在用户的角度来考虑，如果一个网页加载速度有点长的话，那么我们会放弃浏览该网站，所以 PWA 在这一点上做的很好，他的加载速度是很快的。

Engaging ： PWA 可以添加在用户的主屏幕上，不用从应用商店进行下载，他们通过网络应用程序 Manifest file 提供类似于 APP 的使用体验（ 在 Android 上可以设置全屏显示哦，由于 Safari 支持度的问题，所以在 IOS 上并不可以 ），并且还能进行 ”推送通知” 。  



**Application Demo -- 应用演示**

[[Aodabo]](https://aodabo.tech) has applied PWA through following guide. Check the [[Aodabo]](https://aodabo.tech) through your iPhone of Android. Add it to your home screen, and you can use it as a normal APP.  

[[Aodabo | 凹大卜]](https://aodabo.tech)已经通过如下教程应用了PWA技术。你可以从苹果或安卓手机端来查看[[Aodabo | 凹大卜]](https://aodabo.tech)，并添加到主屏幕当做正常的应用程序使用。  

*Add to Home Screen -- 添加应用至桌面*

![add](https://i.postimg.cc/Rhn2qhP8/addtohome.png)  



*APP Screenshot -- 应用程序截图*

![app](https://i.postimg.cc/Px7BtYMY/homeapp.png)  





**Build Guide -- 搭建方法**

*Code to register the service worker -- 注册服务service worker*

This is the first step to making your web app work offline. Copy and paste this code to your index file, **e.g** just before the end of the body tag or in the head tag in html5. You can only register service workers on Websites, Web Apps or Pages served over HTTPS.  

第一步将js代码复制粘贴到你的index页面中，位置可以在末尾`body`标签之前，或者在头部`head`标签之中。注意，你只能通过`HTTPS`来注册service worker到你的网页应用中。  

```js
    <!-- register service worker -->
        <script>

          if ('serviceWorker' in navigator) 
          {
          window.addEventListener('load', function() {
            navigator.serviceWorker.register('/service-worker.js')
            .then(function() { console.log("Service Worker Registered, Cheers to PWA Fire!"); });
          }
          );
        }  
        </script> 
    <!-- end of service worker -->
```

  

*Using the web manifest -- 使用网页manifest*  

Add a link tag to all the pages that encompass your web app, as follows;  

加入`link`标签到你的网页中来包含manifest:

```html
      <!-- start of web manifest -->
      <link rel="manifest" href="/manifest.json">
      <!-- end of web manifest -->
```

根据情况编辑`manifest.json`并放到网站项目的根目录，例如：

```json
{
  "name": "一个PWA示例",
  "short_name": "PWA示例",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#fff",
  "theme_color": "#3eaf7c",
  "icons": [
    {
      "src": "/youhun.jpg",
      "sizes": "120x120",
      "type": "image/png"
    }
  ],
}
```

或者可以通过[[manifest在线生成工具]](https://pwafire.org/developer/tools/get-manifest/)帮助生成`manifest.json`。  



*service-worker.js config -- service worker配置*

Follow the steps as commented in the code below in order to correctly configure the `service-worker.js` file.  

根据标注的提示来修改一下代码完成`service-worker.js`的配置，并放在网站项目的根目录下：

```javascript
// after a service worker is installed and the user navigates to a different page or 
// refreshes,the service worker will begin to receive fetch events
//安装完service worker后,刷新页面或者导航到其他页面,service worker将开始接受和获取事件events
self.addEventListener('fetch', function(event) {
event.respondWith(caches.open('cache').then(function(cache) {
return cache.match(event.request).then(function(response) {
console.log("cache request: " + event.request.url);
var fetchPromise = fetch(event.request).then(function(networkResponse) {           
// if we got a response from the cache, update the cache                   
// 如果我们从缓存cache中得到响应response,更新缓存cache
console.log("fetch completed: " + event.request.url, networkResponse);
if (networkResponse) {
    console.debug("updated cached page: " + event.request.url, networkResponse);
      cache.put(event.request, networkResponse.clone());}
        return networkResponse;
          }, function (event) {   
// rejected promise - just ignore it, we're offline!   
          console.log("Error in fetch()", event);
          event.waitUntil(
          caches.open('cache').then(function(cache) { 
// our cache is named *cache* in the caches.open() above
          return cache.addAll
          ([            
//cache.addAll(), takes a list of URLs, then fetches them from the server
// and adds the response to the cache.           
// add your entire site to the cache- as in the code below; for offline access
// If you have some build process for your site, perhaps that could 
// generate the list of possible URLs that a user might load. 

// cache.addAll()会包含一列链接URLs,然后获取他们并加入对缓存的响应
// 通过修改下面列表加入你的整个网站到缓存中,以供离线使用
// 如果你的网站有一些建设过程,或许可以生成一列可能用户会载入的URL链接
        '/', // do not remove this
        '/index.html', //default
        '/index.html?homescreen=1', //default
        '/?homescreen=1', //default
        '/assets/css/main.css',// configure as by your site ; just an example
        '/images/*',// choose images to keep offline; just an example
// Do not replace/delete/edit manifest.js paths below
        '/manifest.js',
//These are links to the extenal social media buttons that should be cached;
// we have used twitter's as an example
        'https://platform.twitter.com/widgets.js',       
        ]);
        })
        );
        });
// respond from the cache, or the network
  return response || fetchPromise;
});
}));
});

self.addEventListener('install', function(event) {
  // The promise that skipWaiting() returns can be safely ignored.
  self.skipWaiting();
  console.log("Latest version installed!");
});
```

After deployment, we can test it through Chrome Browser's Dev Tools -- Audits / Application to see if it work.

这样就完成部署了，可以使用Chrome浏览器的Dev Tools -- Audits 或 Application工具进行测试。



*Node modules production ready service-worker.js — Node模块的service-worker.js*

If you are using `node modules` for your web application, then we got your back to help you get started. We are going to use [workbox](https://developers.google.com/web/tools/workbox/). Read more on [workbox](https://developers.google.com/web/tools/workbox/) and [latest releases here](https://github.com/GoogleChrome/workbox/releases/tag/v3.3.0).

如果你的网站应用使用`node.js`搭建的，我们将会使用 [workbox](https://developers.google.com/web/tools/workbox/)来部署service worker, [[最新发布的service worker]](https://github.com/GoogleChrome/workbox/releases/tag/v3.3.0)。

Create an empty js file; `service-worker.js` in your source root. In the empty source service worker file, say `src/service-worker.js` ; add the following code snippet: Go through the comments.  

建立一个空的`service-worker.js`到你资源的根目录,例如`src/service-worker.js`, 加入以下代码:

~~~js

importScripts('https://storage.googleapis.com/workbox-cdn/releases/3.6.2/workbox-sw.js')
if (workbox) {
console.log(`Yay! Workbox is loaded ! Cheers to PWA Fire 🐹`);
workbox.precaching.precacheAndRoute([]);
} else {
console.log(`Oops! Workbox didn't load 👺`);
}

~~~

Create an empty `sw-config.js` file in your project's root folder. Add the code snippet below to it; configure to add which files you want to `precache`.  

建立`sw-config.js`到你项目的根目录,根据情况编辑以下代码,并加入你想预先缓存的文件：

```js
      
module.exports = {
"globDirectory": "build/", // The base directory you wish to match globPatterns against, 
// relative to the current working directory.
"globPatterns": [
// edit to add all file to cache; configure for your project below
// 编辑加入你所有想要缓存的文件
"**/*.css", // eg cache all css files, images etc in the root folder
"index.html",
"images/*.jpg"
],
"swSrc": "src/service-worker.js", // The path and filename of the service worker file that will be created by the build process.
"swDest": "build/service-worker.js", // The path to the source service worker file that can contain your own customized code,
// in addition to containing a match for injectionPointRegexp.
"globIgnores": [
"../sw-config.js"
]
};
```

Open your `package.json` and update the build script to run the `Workbox` *injectManifest* command. Add `workbox injectManifest sw-config.js` infront of `your-build-process-task` . The updated `package.json` should look like the following:  

打开你的`package.json`更新build代码来运行Workbox 和 injectManifest 指令，代码更新如下： 

```markup
{
	"scripts": 
		{
			"build": "your-build-process-task && workbox injectManifest sw-config.js"
		}
}
```

`workbox-cli`  is a command-line tool that allows us to inject a file manifest into a source service worker file. Add it to your `devDependencies` as in the code below:  

`workbox-cli`是一个命令行工具，允许我们注入manifest文件到service worker文件。将他加入到	`devDependencies`中，如下： 

```markup
{
	"devDependencies": {
		"workbox-cli": "^3.0.1"
	}
}
```

The *precacheAndRoute* call in `build/service-worker.js` has been updated. In your text editor, open `build/service-worker.js` and observe that  your files to cache are included in the file manifest.

在`build/service-worker.js`中的`precacheAndRoute`应该已经更新。打开并查看你的缓存文件是否已经在manifest文件中。至此我们的PWA应用就完成部署了。