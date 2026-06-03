# Estructura de `ClientAngularApp`

Es una app Angular **21** con **SSR (Server-Side Rendering)** habilitado, usando el builder moderno `@angular/build:application`.

---

## Árbol de directorios y archivos

```
ClientAngularApp/
├── public/                     ← Activos estáticos servidos en la raíz
│   └── favicon.ico
│
├── src/                        ← Código fuente de la aplicación
│   ├── app/                    ← Núcleo de la app Angular
│   │   ├── app.ts              ← Componente raíz (App)
│   │   ├── app.html            ← Template del componente raíz
│   │   ├── app.css             ← Estilos del componente raíz
│   │   ├── app.config.ts       ← Configuración para el navegador (providers)
│   │   ├── app.config.server.ts← Configuración para el servidor (SSR)
│   │   ├── app.routes.ts       ← Rutas del cliente
│   │   ├── app.routes.server.ts← Rutas del servidor (render mode)
│   │   └── app.spec.ts         ← Tests del componente raíz
│   │
│   ├── index.html              ← HTML shell; contiene <app-root>
│   ├── main.ts                 ← Entry point del navegador (bootstrap)
│   ├── main.server.ts          ← Entry point del servidor (SSR bootstrap)
│   ├── server.ts               ← Servidor Express + Angular SSR engine
│   ├── styles.css              ← Estilos globales de la app
│   └── material-theme.scss     ← Tema de Angular Material
│
├── node_modules/               ← Dependencias instaladas (npm)
│
├── angular.json                ← Configuración del workspace y builders
├── package.json                ← Dependencias y scripts npm
├── package-lock.json           ← Lock file de versiones exactas
├── tsconfig.json               ← Configuración TypeScript base
├── tsconfig.app.json           ← TS config para la app (excluye specs)
├── tsconfig.spec.json          ← TS config para tests (vitest)
├── .editorconfig               ← Estilo de código para el editor
├── .prettierrc                 ← Configuración de Prettier
├── .gitignore
└── .vscode/                    ← Configuración de VS Code
    ├── extensions.json
    ├── launch.json
    ├── mcp.json
    └── tasks.json
```

---

## Descripción de los archivos clave

### Archivos de configuración raíz

| Archivo | Rol |
|---|---|
| `angular.json` | Workspace Angular: define el builder (`@angular/build:application`), los entry points (`src/main.ts`, `src/main.server.ts`, `src/server.ts`), los estilos globales, budgets de tamaño y configuraciones de build |
| `package.json` | Dependencias npm (Angular 21, Material, Express, RxJS) y scripts: `start`, `build`, `test`, `serve:ssr:ClientAngularApp` |
| `tsconfig.json` | Config TypeScript base con `strict: true`, target `ES2022`, `module: preserve`, y opciones estrictas del compilador Angular |
| `tsconfig.app.json` | Extiende `tsconfig.json`; incluye `src/**/*.ts` y excluye los `.spec.ts` |
| `tsconfig.spec.json` | Extiende `tsconfig.json`; solo incluye archivos `.spec.ts` y `.d.ts`, con tipos de `vitest/globals` |

---

### `src/` — Entry points y estilos

| Archivo | Rol |
|---|---|
| `index.html` | HTML shell de la SPA; contiene `<app-root>` donde Angular monta la app; carga fuentes de Google (Roboto, Material Icons) |
| `main.ts` | Entry point del **navegador**; llama a `bootstrapApplication(App, appConfig)` para arrancar la app en el cliente |
| `main.server.ts` | Entry point del **servidor (SSR)**; exporta una función `bootstrap` que usa la configuración de servidor (`app.config.server.ts`) |
| `server.ts` | Servidor **Express** con el engine `AngularNodeAppEngine`; sirve estáticos desde `dist/browser` y delega el resto a Angular SSR; escucha en el puerto `PORT` (default 4000) |
| `styles.css` | Estilos CSS globales de la app |
| `material-theme.scss` | Tema de Angular Material (SCSS); se compila antes que `styles.css` según `angular.json` |

---

### `src/app/` — Núcleo de la aplicación

| Archivo | Rol |
|---|---|
| `app.ts` | Componente raíz `App`; usa `signal()` para el título y `RouterOutlet` para renderizar rutas |
| `app.html` | Template HTML del componente raíz |
| `app.css` | Estilos encapsulados del componente raíz |
| `app.config.ts` | Configuración de la app para el **navegador**: registra `provideRouter(routes)` y `provideClientHydration(withEventReplay())` para hidratación SSR |
| `app.config.server.ts` | Configuración para el **servidor**: fusiona `appConfig` con `provideServerRendering(withRoutes(serverRoutes))` usando `mergeApplicationConfig` |
| `app.routes.ts` | Define las rutas del cliente (`Routes[]`); actualmente vacío — aquí se añadirán las rutas de la SPA |
| `app.routes.server.ts` | Define el modo de render en el servidor; el wildcard `**` usa `RenderMode.Prerender` |
| `app.spec.ts` | Tests unitarios del componente raíz, ejecutados con Vitest |

---

## Flujo de arranque según el contexto

```
Navegador:
  index.html → main.ts → bootstrapApplication(App, appConfig)
                                                      └── app.config.ts (router + hydration)

Servidor (SSR):
  angular.json → server.ts (Express) → main.server.ts → bootstrap(App, config)
                                                                         └── app.config.server.ts
                                                                               = appConfig + serverRendering
```

---

## Dependencias principales

| Paquete | Versión | Propósito |
|---|---|---|
| `@angular/core` + suite | ^21.2.0 | Framework base |
| `@angular/material` + `@angular/cdk` | ^21.2.13 | Componentes UI |
| `@angular/ssr` + `@angular/platform-server` | ^21.2.13 | Server-Side Rendering |
| `express` | ^5.1.0 | Servidor Node.js para SSR |
| `rxjs` | ~7.8.0 | Programación reactiva |
| `vitest` | ^4.0.8 | Runner de tests (reemplaza Karma/Jest) |
| `prettier` | ^3.8.1 | Formateo de código |
| `typescript` | ~5.9.2 | Lenguaje base |
