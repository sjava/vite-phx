# phoenix-with-vite
Vite setup for phoenix framework.   
The goal of this approach is to touch phoenix project as less as possible.  

First, `yarn add -D vite chokidar`, and this is all we need:  


```diff
--- a/assets/package.json
+++ b/assets/package.json
@@ -3,8 +3,8 @@
   "description": " ",
   "license": "MIT",
   "scripts": {
+    "build": "vite build",
+    "watch": "chokidar js css -c 'vite build --minify false --emptyOutDir false --clearScreen false --mode development'"
   },
   "devDependencies": {
     "autoprefixer": "^10.2.4",
+    "chokidar-cli": "^2.1.0",
+    "vite": "^2.2.4"
   }
 }
 ```

```diff
--- /dev/null
+++ b/assets/vite.config.js
@@ -0,0 +1,19 @@
+export default {
+  publicDir: "./static",
+  build: {
+    target: "es2018",
+    minify: true,
+    outDir: "../priv/static",
+    emptyOutDir: true,
+    rollupOptions: {
+      input: ["js/app.js", "css/app.css"],
+      output: {
+        entryFileNames: "js/[name].js",
+        chunkFileNames: "js/[name].js",
+        assetFileNames: "[ext]/[name][extname]"
+      }
+    },
+    assetsInlineLimit: 0
+  }
+}
```

```diff
--- a/config/dev.exs
+++ b/config/dev.exs
@@ -23,7 +23,7 @@ config :fset, FsetWeb.Endpoint,
   code_reloader: true,
   check_origin: false,
   watchers: [
-    node: ["whatever", "ever", cd: Path.expand("../assets", __DIR__)]
+    yarn: ["run", "watch", cd: Path.expand("../assets", __DIR__)]
   ]
```

```diff
--- a/Dockerfile
+++ b/Dockerfile
@@ -27,7 +27,7 @@ ENV NODE_ENV=production
 COPY lib lib
 COPY priv priv
 COPY assets assets
+RUN yarn --cwd ./assets run build
 RUN mix phx.digest
```
