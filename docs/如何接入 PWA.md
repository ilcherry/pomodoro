要把你的 **番茄时钟应用**（`https://ilcherry.github.io/pomodoro/`）改成 **PWA（渐进式 Web 应用）**，需要完成以下步骤：

---

## **📌 1. 添加 `manifest.json` 文件**

PWA 需要一个 `manifest.json` 来描述应用的图标、名称等信息。

### **🔹 创建 `manifest.json`（放在项目根目录）**

新建 `manifest.json`，写入以下内容：

```json
{
  "name": "Pomodoro Timer",
  "short_name": "Pomodoro",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#ff5722",
  "icons": [
    {
      "src": "icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

> **🔹 说明**
>
> - `name` / `short_name`：应用名称
> - `start_url`：PWA 启动时加载的页面
> - `display: standalone`：全屏运行，去掉浏览器 UI
> - `theme_color` / `background_color`：设置 UI 颜色
> - `icons`：应用图标（需要手动添加）

---

## **📌 2. 添加应用图标**

在 **项目目录下创建 `icons/` 文件夹**，然后添加两个图标：

- `icons/icon-192x192.png`
- `icons/icon-512x512.png`

> **🎨 生成 PWA 图标工具**
> 可以使用 [Favicon Generator](https://www.favicon-generator.org/) 或 [RealFaviconGenerator](https://realfavicongenerator.net/) 生成 PWA 适配的图标。

---

## **📌 3. 在 `index.html` 中引入 `manifest.json`**

在 `index.html` 的 `<head>` 部分添加：

```html
<link rel="manifest" href="manifest.json" />
<meta name="theme-color" content="#ff5722" />
```

---

## **📌 4. 创建 `service-worker.js`**

PWA 需要 `service worker` 来支持 **离线缓存**。

### **🔹 创建 `service-worker.js`（放在项目根目录）**

```js
const CACHE_NAME = "pomodoro-cache-v1";
const urlsToCache = [
  "./",
  "./index.html",
  "./manifest.json",
  "./icons/icon-192x192.png",
  "./icons/icon-512x512.png",
  "./style.css", // 确保 CSS 也被缓存
  "./script.js", // 确保 JS 也被缓存
];

// 安装 Service Worker 并缓存资源
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches
      .open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
      .then(self.skipWaiting())
  );
});

// 拦截请求，使用缓存
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches
      .match(event.request)
      .then((response) => response || fetch(event.request))
  );
});

// 更新 Service Worker
self.addEventListener("activate", (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    })
  );
});
```

---

## **📌 5. 在 `index.html` 中注册 `service-worker.js`**

在 `index.html` 的 `<script>` 末尾添加：

```html
<script>
  if ("serviceWorker" in navigator) {
    window.addEventListener("load", () => {
      navigator.serviceWorker
        .register("./service-worker.js")
        .then((reg) => console.log("Service Worker 注册成功:", reg.scope))
        .catch((err) => console.error("Service Worker 注册失败:", err));
    });
  }
</script>
```

---

## **📌 6. 提交代码并部署**

```bash
git add .
git commit -m "添加 PWA 支持"
git push origin main
```

GitHub Pages **会自动部署**，几分钟后你就能在 `https://ilcherry.github.io/pomodoro/` 访问 PWA 版的番茄时钟。

---

## **📌 7. 测试 PWA**

### **🔹 在 Chrome DevTools 里测试**

1. **打开 Chrome 开发者工具**（F12 或 `Ctrl + Shift + I`）
2. **切换到 "Application" 选项卡**
3. **检查 `manifest.json` 是否正确**
4. **检查 `Service Worker` 是否激活**
5. **断网后尝试访问，看看是否能离线使用**

---

## **🎉 8. 你现在有一个 PWA 番茄时钟了！**

✅ **可添加到手机桌面**（类似原生 App）  
✅ **支持离线访问**  
✅ **全屏模式**  
✅ **提升用户体验** 🚀

你可以访问 `https://ilcherry.github.io/pomodoro/`，然后：

- **PC 端**：Chrome 地址栏右侧会出现 "安装" 按钮
- **手机端**：点击 **"添加到主屏幕"**，它就像一个 App 一样了！🎯

如果有问题或者需要优化，欢迎交流！🔥
