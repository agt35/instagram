# Instagram Clone Project

## Project Overview

This project was used to solifify my learnings on Vue.js Framework.

### Technologies used
- Vue: Used as the development framework
- Supabase: Used this backend as a service to implement users and authentication, posts storage, and to keep track of followers
- Ant Design Vue: This UI Component library was used to implment some components, such as Cards, Modals, and loading Spinners.
- Pinia: Used for global state management

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Run Unit Tests with [Vitest](https://vitest.dev/)

```sh
npm run test:unit
```

### Run End-to-End Tests with [Cypress](https://www.cypress.io/)

```sh
npm run test:e2e:dev
```

This runs the end-to-end tests against the Vite development server.
It is much faster than the production build.

But it's still recommended to test the production build with `test:e2e` before deploying (e.g. in CI environments):

```sh
npm run build
npm run test:e2e
```

**Note:** Supabase needs to be set up and connected to this project
