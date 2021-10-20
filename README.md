<h1 align="center">Welcome 😀</h1>

<p align="left">
  <a href="https://github.com/actions/setup-node"><img alt="GitHub Actions status" src="https://github.com/activeguild/classnames-generics/workflows/automatic%20release/badge.svg" style="max-width:100%;"></a>
</p>

# vite-plugin-sass-dts
 This is a plugin that automatically creates a type file when using the css module type-safely.

## install
```bash
npm i -D vite-plugin-sass-dts
```

## Options

| Parameter | Type             | Description                                                                     |
| --------- | ---------------- | ------------------------------------------------------------------------------- |
| allGenerate | boolean | Create all d.ts files of the css, sass, scss files included in the project at build time.<br />We recommend that you turn off the flag once you have created the d.ts file, as it will take a long time to build.                            |
| global.generate | boolean | Outputs the common style set in <b>additionalData</b> of <b>preprocessorOptions</b> as a global type definition file. |
| global.outFile | string | Specify the file that outputs the global common style with an absolute path.Relative paths will be supported. |
## Add it to vite.config.ts

```ts
import { defineConfig } from "vite";
import sassDts from "vite-plugin-sass-dts";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [sassDts()],
});

```

## Usage
You can create a dts file by saving the scss file during development.
You can check the usage [example](https://github.com/activeguild/vite-plugin-sass-dts/tree/master/example) when the following options are set.
Prepare the vite.config.ts file with the following options and start it in development mode.

```ts
[vite.config.ts]

import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import sassDts from "vite-plugin-sass-dts";
import path from "path";

export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@use "@/stylesheets/variables" as var;`,
        importer(...args) {
          if (args[0] !== "@/stylesheets/variables") {
            return;
          }

          return {
            file: `${path.resolve(
              __dirname,
              "./src/assets/stylesheets/variables"
            )}`,
          };
        },
      },
    },
  },
  plugins: [
    react(),
    sassDts({
      allGenerate: true,
      global: {
        generate: true,
        outFile: path.resolve(__dirname, "./src/style.d.ts"),
      },
    }),
  ],
});
```

```bash
npm run dev
```

Then save the following file ...


```scss
[src/assets/stylesheets/variables/_index.scss]

.row {
  display: flex;
}
```

```scss
[src/App.module.scss]

.header {
  background-color: var.$primary;
  .active {
    background-color: black;
  }
}
```

Saving the scss file creates a d.ts file in the same hierarchy.

```ts
[src/App.scss.d.ts]

export * from './style.d'
import { GlobalClassNames } from './style.d'

export const header = 'header'
export const active = 'active'

export type ClassNames = 'header' | 'active' | GlobalClassNames
```


The type definition is output to the output path of the common style specified in the option.

```ts
[src/style.d.ts]

export const row = 'row'

export type GlobalClassNames = 'row'
```

