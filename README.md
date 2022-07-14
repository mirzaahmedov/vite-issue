# Reproduction [#9109](https://github.com/vitejs/vite/issues/9109)

## Steps to reproduce

1. Install vitejs React template
```shell
$ npm create vite@latest .
$ npm install
```

2. Edit vite.config.js
```diff
+import path from "path";
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
+  resolve: {
+    alias: {
+      "@views": path.resolve(__dirname, "src/views"),
+      "@components": path.resolve(__dirname, "src/components"),
+      "@assets": path.resolve(__dirname, "src/assets"),
+      "@utils": path.resolve(__dirname, "src/utils"),
+      "@hooks": path.resolve(__dirname, "src/hooks"),
+      "@context": path.resolve(__dirname, "src/context"),
+    },
+  },
});
```

3. Run dev server
```shell
$ npm run dev

failed to load config from /home/mirzaahmedov/Web/Svelte/Learn/vite.config.js
error when starting dev server:
ReferenceError: __dirname is not defined in ES module scope
This file is being treated as an ES module because it has a '.js' file extension and '/home/mirzaahmedov/Web/Svelte/Learn/package.json' contains "type": "module". To treat it as a CommonJS script, rename it to use the '.cjs' file extension.
    at file:///home/mirzaahmedov/Web/Svelte/Learn/vite.config.js?t=1657820307052:10:30
    at ModuleJob.run (node:internal/modules/esm/module_job:198:25)
    at async Promise.all (index 0)
    at async ESMLoader.import (node:internal/modules/esm/loader:409:24)
    at async loadConfigFromFile (file:///home/mirzaahmedov/Web/Svelte/Learn/node_modules/vite/dist/node/chunks/dep-561c5231.js:62653:31)
    at async resolveConfig (file:///home/mirzaahmedov/Web/Svelte/Learn/node_modules/vite/dist/node/chunks/dep-561c5231.js:62281:28)
    at async createServer (file:///home/mirzaahmedov/Web/Svelte/Learn/node_modules/vite/dist/node/chunks/dep-561c5231.js:58899:20)
    at async CAC.<anonymous> (file:///home/mirzaahmedov/Web/Svelte/Learn/node_modules/vite/dist/node/cli.js:699:24)
```

4. Add __filename and __dirname variables to vite.config.js
```diff
import path from "path";
+import * as url from "url";
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

+const __filename = url.fileURLToPath(import.meta.url);
+const __dirname = path.dirname(__filename);

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@views": path.resolve(__dirname, "src/views"),
      "@components": path.resolve(__dirname, "src/components"),
      "@assets": path.resolve(__dirname, "src/assets"),
      "@utils": path.resolve(__dirname, "src/utils"),
      "@hooks": path.resolve(__dirname, "src/hooks"),
      "@context": path.resolve(__dirname, "src/context"),
    },
  },
});
```

5. Gives another error
```shell
✘ [ERROR] The symbol "__filename" has already been declared

    vite.config.js:6:6:
      6 │ const __filename = url.fileURLToPath(import.meta.url);
        ╵       ~~~~~~~~~~

  The symbol "__filename" was originally declared here:

    vite.config.js:1:62:
      1 │ ...edov/Web/Svelte/Learn";const __filename = "/home/mirzaahmedov/Web/S...
        ╵                                 ~~~~~~~~~~

✘ [ERROR] The symbol "__dirname" has already been declared

    vite.config.js:7:6:
      7 │ const __dirname = path.dirname(__filename);
        ╵       ~~~~~~~~~

  The symbol "__dirname" was originally declared here:

    vite.config.js:1:6:
      1 │ const __dirname = "/home/mirzaahmedov/Web/Svelte/Learn";const __filena...
        ╵       ~~~~~~~~~

failed to load config from /home/mirzaahmedov/Web/Svelte/Learn/vite.config.js
error when starting dev server:
Error: Build failed with 2 errors:
vite.config.js:6:6: ERROR: The symbol "__filename" has already been declared
vite.config.js:7:6: ERROR: The symbol "__dirname" has already been declared
    at failureErrorWithLog (/home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:1605:15)
    at /home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:1251:28
    at runOnEndCallbacks (/home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:1034:63)
    at buildResponseToResult (/home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:1249:7)
    at /home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:1358:14
    at /home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:666:9
    at handleIncomingPacket (/home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:763:9)
    at Socket.readFromStdout (/home/mirzaahmedov/Web/Svelte/Learn/node_modules/esbuild/lib/main.js:632:7)
    at Socket.emit (node:events:537:28)
    at addChunk (node:internal/streams/readable:324:12)
```

!!!Removing `type: module` from `package.json` solves the problem
