# Vite-Env-Only Reproduction

Reproduction for https://github.com/pcattori/vite-env-only/issues/52.

## Reproduction steps

1. `git clone https://github.com/stigi/vite-env-repro.git ; cd vite-env-repro ; npm install`
2. `npm run dev`
3. Open http://localhost:5173/

### Expected

- No `vite-env-only` error in the console
- `{ isbot: false, isSSR: true }` printed to the console, demonstrating that the usage is actually in the server (SSR) environment.

### Actual

Error, from `index.html` showing `isbot` usage in `client` environment:

```
Error: [vite-env-only] Import denied
 - Denied by specifier pattern: isbot
 - Importer: index.html
 - Import: "isbot"
 - Environment: client
    at ResolveIdContext.resolveId (file:///Users/ullrich/tmp/x/vite-env-repro/node_modules/vite-env-only/dist/index.js:153:15)
    at EnvironmentPluginContainer.resolveId (file:///Users/ullrich/tmp/x/vite-env-repro/node_modules/vite/dist/node/chunks/dep-DBxKXgDP.js:42213:17)
    at EnvironmentModuleGraph._resolveUrl (file:///Users/ullrich/tmp/x/vite-env-repro/node_modules/vite/dist/node/chunks/dep-DBxKXgDP.js:47715:41)
    at EnvironmentModuleGraph.getModuleByUrl (file:///Users/ullrich/tmp/x/vite-env-repro/node_modules/vite/dist/node/chunks/dep-DBxKXgDP.js:47476:19)
    at async Promise.all (index 0)
    at ModuleGraph.getModuleByUrl (file:///Users/ullrich/tmp/x/vite-env-repro/node_modules/vite/dist/node/chunks/dep-DBxKXgDP.js:37922:39)
    at addFromUrl (/Users/ullrich/tmp/x/vite-env-repro/node_modules/@remix-run/dev/dist/vite/styles.js:147:16)
    at async Promise.all (index 2)
    at findDeps (/Users/ullrich/tmp/x/vite-env-repro/node_modules/@remix-run/dev/dist/vite/styles.js:159:3)
    at getStylesForFiles (/Users/ullrich/tmp/x/vite-env-repro/node_modules/@remix-run/dev/dist/vite/styles.js:103:7)
```

## Steps to create this repo

1. `npx create-remix@latest vite-env-repro ; cd vite-env-repro`
2. `npm install -D vite-env-only`
3. Editing vite config, adding example config from the vite-env-only Readme, denying isbot on client
```
    denyImports({
      client: {
        specifiers: ["fs-extra", /^node:/, "isbot"],
        files: ["**/.server/*", "**/*.server.*"],
      },
      server: {
        specifiers: ["jquery"],
      },
    }),
```
4. Adding usage of `isbot` to `root.tsx`.
```
export async function loader() {
  return {
    isbot: isbot(),
    isSSR: import.meta.env.SSR,
  }
}
```

Can also be followed in the commit history.
