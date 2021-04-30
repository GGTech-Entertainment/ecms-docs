# **Admin GTS**

## **Quick guide**
Checkout **[project setup](#project-setup)** for a quick dive into the project.
Checkout **[project structure](#project-structure)**, **[libraries](#libraries)** and **[best practices](#best-practices)** for a better understanding of the project
**[Technical debts](#technical-debts)** section is intended to list **quick fixes or components that need code improvements**

<br/>

## **Table of contents**
1. [Intro](#intro)
2. [Project setup](#project-setup)
3. [Project structure](#project-structure)
4. [Libraries](#libraries)
5. ~~Components~~ *(WIP)*
6. [Best practices](#best-practices)
7. [Technical debts](#technical-debts)

## **Intro** <span id="intro"></span>

This project relates to the CMS only, and is built with Vue.js V3 and Vite, you can find more details in [Libraries section](#libraries).

## **Project setup** <span id="project-setup"></span>

>For local environment variables, create `.env.[mode].local` files e.g. `.env.development.local` or `.env.local`

```bash
npm install
```

><span style="color: tomato">* Please remember to run the above command **after pulling changes from develop** branch</span>

### **Development server** with hot-reloads
```bash
npm run dev
```
The app will work using environment variables declared on `.env` and `.env.development` files.

<br/>

### **Pre-production build** (compile and minify)
```bash
npm run build:pre
```
The app will work using environment variables declared on `.env` and `.env.pre`.


### **Production build** (compile and minify)
```bash
npm run build
```
The app will work using environment variables declared on `.env` and `.env.production`.

### Locally preview production and pre-prod builds
```bash
npm run serve
```

### Lints and fixes files
```bash
npm run lint
```

## **Project structure** <span id="project-structure"></span>

Only relevant folders and files will be described in this section, this is because **project structure and code should be self-explanatory**

```bash
/.vscode  # Global vscode settings and useful snippets
/dist  # Production/Pre-production build files
/node_modules # Installed packages
/public # Static assets
/src  # Main project folder
  /assets # Images, videos, fonts, etc
  /components # Reusable Vue components
  /layouts  # Vue components for layouts
  /lib  # Javascript assets such as composables, custom validations, etc
  /locales  # JSON files for translations (1 file per language)
  /router # Vue router modules
  /store  # Vuex modules
  /styles # Scss styles
  /views  # Vue components for views
  App.vue # Vue app main template
  i18n.js # Constructor for vue i18n plugin
  main.js # Vue app constructor
.env  # Default environment variables
.env.pre  # Pre-production environment variables
.env.prod # Production environment variables
.eslintrc.js  # Eslint config file
.gitignore  # Git ignore files and folders
.lintstagedrc.js  # Commands to run before git commit
.simple-git-hooks.js  # git hooks enabler
.jest.config.js # Jest unit tests config file
jsconfig.json # Javascript config file
package.json  # File containing project's installed libraries
README.md # You are here 🚩
vite.config.js # Vite config file
```

> ### **How layouts/views/components should work**
> layout > views > components

## **Libraries** <span id="libraries"></span>

| Library | Description |
| --- | --- |
| [Vue v3](https://v3.vuejs.org/) | Project's framework |
| [Vite v2](https://vitejs.dev/) | Build tool |
| [Vuex](https://next.vuex.vuejs.org/) | App's global state management library |
| [Vue Router](https://next.router.vuejs.org/) | App's routing library |
| [Sass](https://sass-lang.com/) | SCSS styling |
| [Axios](https://axios-http.com/) | Promised based HTTP client |
| [Luxon](https://moment.github.io/luxon/) | DateTime handler |
| [Vue i18n](https://vue-i18n.intlify.dev/introduction.html) | Localization |
| [Eslint](https://eslint.org/) | Coding rules |
| [Jest](https://jestjs.io/) | Testing framework |
| [lint-staged](https://github.com/okonet/lint-staged) | Run commads on staged git files |
| [simple-git-hooks](https://github.com/toplenboren/simple-git-hooks) | Manages git hooks in all platforms. Works with `simple-git-hooks` |

## **Best Practices** <span id="best-practices"></span>

* On any front-end framework, learn the [lifecycle hooks](https://v3.vuejs.org/guide/instance.html#lifecycle-hooks) and how to use them.
* **Use functional programming**. It feels more natural in Javascript, go ask [Facebook and the React team](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines).
* Use EcmaScript's latest features.
* Project structure and code must be self-explanatory.
* Avoid [code smells](https://www.google.com/search?q=code+smells+javascript).
* DRY (Don't Repeat Yourself).
* KISS (Keep It Simple, S...).
* Nested `if` / `if else` are not allowed.
* `!important` is not allowed.
* \<br /\> not allowed.

## **Technical Debts** <span id="technical-debts"></span>

### **Unit testing**
By the moment of writing Jest is configured but there are no tests written for components.

### **PWA**
There are [plugins available](https://github.com/antfu/vite-plugin-pwa) for enabling PWA. This is important as it will enable poweful features, such as:
* Service workers with offline support
* Prompt for new content refreshing
* Automatic reload when new content available
* Site installer for mobiles
