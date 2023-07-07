## 搭建后台管理系统模板

一个项目要有统一的规范.

- 需要使用`eslint+stylelint+prettier`来对我们的代码质量做检测和修复。
- 需要使用`husky`来做 commit 拦截。
- 需要使用`commitlint`来统一提交规范。
- 需要使用`preinstall`来统一包管理工具。

### 项目初始化

```bash
# 使用配置项构建项目
yarn create vue
# 只安装vue和vite，其它配置手动搭建项目
yarn create vite
```

### 项目配置

#### ESLint

##### 安装 ESLint

```bash
# 安装eslint
yarn add -D eslint
# 生成配置文件：.eslintrc.cjs
npx eslint --init
```

##### .eslintrc.cjs 配置文件

```cjs
module.exports = {
  //运行环境
  env: {
    browser: true, //运行于浏览器端
    es2021: true, //使用es2021
  },
  //规则继承
  extends: [
    //全部规则默认是关闭的,这个配置项开启推荐规则,推荐规则参照文档
    'eslint:recommended',
    //ts语法规则
    'plugin:@typescript-eslint/recommended',
    //vue3语法规则
    'plugin:vue/vue3-essential',
  ],
  //要为特定类型的文件指定处理器
  overrides: [
    {
      env: {
        node: true,
      },
      files: ['.eslintrc.{js,cjs}'],
      parserOptions: {
        sourceType: 'script',
      },
    },
  ],
  //指定解析器选项
  parserOptions: {
    ecmaVersion: 'latest', //校验ECMA最新版本
    //指定解析器:解析器
    //Esprima 默认解析器
    //Babel-ESLint babel解析器
    //@typescript-eslint/parser ts解析器
    parser: '@typescript-eslint/parser',
    //设置为"script"（默认），或者"module"代码在ECMAScript模块中
    sourceType: 'module',
  },
  //ESLint支持使用第三方插件。在使用插件之前，您必须使用npm安装它
  //该eslint-plugin-前缀可以从插件名称被省略
  plugins: ['@typescript-eslint', 'vue'],
  //eslint规则
  rules: {},
}
```

##### vue3 环境代码校验插件

```js
//让所有与prettier规则存在冲突的Eslint rules失效，并使用prettier进行代码检查
"eslint-config-prettier": "^8.6.0",
"eslint-plugin-import": "^2.27.5",
"eslint-plugin-node": "^11.1.0",
//运行更漂亮的Eslint，使prettier规则优先级更高，Eslint优先级低
"eslint-plugin-prettier": "^4.2.1",
//vue.js的Eslint插件（查找vue语法错误，发现错误指令，查找违规风格指南
"eslint-plugin-vue": "^9.9.0",
//该解析器允许使用Eslint校验所有babel code
"@babel/eslint-parser": "^7.19.1",
```

安装指令

```bash
yarn add -D eslint-plugin-import eslint-plugin-vue eslint-plugin-node @babel/eslint-parser
```

##### 修改.eslintrc.cjs 配置文件

```cjs
module.exports = {
  //运行环境
  env: {
    browser: true, //运行于浏览器端
    es2021: true, //使用es2021
  },
  //规则继承
  extends: [
    //全部规则默认是关闭的,这个配置项开启推荐规则,推荐规则参照文档
    'eslint:recommended',
    //ts语法规则
    'plugin:@typescript-eslint/recommended',
    //vue3语法规则
    'plugin:vue/vue3-essential',
  ],
  //要为特定类型的文件指定处理器
  overrides: [
    {
      env: {
        node: true,
      },
      files: ['.eslintrc.{js,cjs}'],
      parserOptions: {
        sourceType: 'script',
      },
    },
  ],
  //指定解析器选项
  parserOptions: {
    ecmaVersion: 'latest', //校验ECMA最新版本
    //指定解析器:指定如何解析语法
    //Esprima 默认解析器
    //Babel-ESLint babel解析器
    //@typescript-eslint/parser ts解析器
    parser: ['@typescript-eslint/parser', 'vue-eslint-parser'],
    //设置为"script"（默认），或者"module"代码在ECMAScript模块中
    sourceType: 'module',
  },
  //ESLint支持使用第三方插件。在使用插件之前，您必须使用npm安装它
  //该eslint-plugin-前缀可以从插件名称被省略
  plugins: ['@typescript-eslint', 'vue'],
  /*
   * eslint规则
   * "off" 或 0    ==>  关闭规则
   * "warn" 或 1   ==>  打开的规则作为警告（不影响代码执行）
   * "error" 或 2  ==>  规则作为一个错误（代码不能执行，界面报错）
   */
  rules: {
    // eslint（https://eslint.bootcss.com/docs/rules/）
    'no-var': 'error', // 要求使用 let 或 const 而不是 var
    'no-multiple-empty-lines': ['warn', { max: 1 }], // 不允许多个空行
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-unexpected-multiline': 'error', // 禁止空余的多行
    'no-useless-escape': 'off', // 禁止不必要的转义字符

    // typeScript (https://typescript-eslint.io/rules)
    '@typescript-eslint/no-unused-vars': 'error', // 禁止定义未使用的变量
    '@typescript-eslint/prefer-ts-expect-error': 'error', // 禁止使用 @ts-ignore
    '@typescript-eslint/no-explicit-any': 'off', // 禁止使用 any 类型
    '@typescript-eslint/no-non-null-assertion': 'off',
    '@typescript-eslint/no-namespace': 'off', // 禁止使用自定义 TypeScript 模块和命名空间。
    '@typescript-eslint/semi': 'off',

    // eslint-plugin-vue (https://eslint.vuejs.org/rules/)
    'vue/multi-word-component-names': 'off', // 要求组件名称始终为 “-” 链接的单词
    'vue/script-setup-uses-vars': 'error', // 防止<script setup>使用的变量<template>被标记为未使用
    'vue/no-mutating-props': 'off', // 不允许组件 prop的改变
    'vue/attribute-hyphenation': 'off', // 对模板中的自定义组件强制执行属性命名样式
  },
}
```

##### .eslintignore 忽略文件

```bash
dist
node_modules
```

##### 运行脚本

在 package.json 中添加运行脚本

```json
"scripts": {
    "lint": "eslint src",
    "fix": "eslint src --fix",
}
```

#### Prettier

##### 安装 Prettier

```bash
yarn add -D eslint-plugin-prettier prettier eslint-config-prettier
```

##### .prettierrc.json 添加规则

```json
{
  "singleQuote": true,
  "semi": false,
  "bracketSpacing": true,
  "htmlWhitespaceSensitivity": "ignore",
  "endOfLine": "auto",
  "trailingComma": "all",
  "tabWidth": 2
}
```

##### .prettierignore 忽略文件

```bash
/dist/*
/html/*
.local
/node_modules/**
**/*.svg
**/*.sh
/public/*
```

##### 修改.eslintrc.cjs 配置文件

```cjs
/* 继承已有的规则，添加如下规则 */
  extends: [
    'plugin:prettier/recommended',
  ],
```

**通过 pnpm run lint 去检测语法，如果出现不规范格式,通过 pnpm run fix 修改**

#### StyleLint

##### 安装 StyleLint

```bash
# 安装sass以及StyleLint
yarn add sass sass-loader stylelint postcss postcss-scss postcss-html stylelint-config-prettier stylelint-config-recess-order stylelint-config-recommended-scss stylelint-config-standard stylelint-config-standard-vue stylelint-scss stylelint-order stylelint-config-standard-scss -D
```

##### .stylelintrc.cjs 添加规则

```cjs
// @see https://stylelint.bootcss.com/

module.exports = {
  extends: [
    'stylelint-config-standard', // 配置stylelint拓展插件
    'stylelint-config-html/vue', // 配置 vue 中 template 样式格式化
    'stylelint-config-standard-scss', // 配置stylelint scss插件
    'stylelint-config-recommended-vue/scss', // 配置 vue 中 scss 样式格式化
    'stylelint-config-recess-order', // 配置stylelint css属性书写顺序插件,
    'stylelint-config-prettier', // 配置stylelint和prettier兼容
  ],
  overrides: [
    {
      files: ['**/*.(scss|css|vue|html)'],
      customSyntax: 'postcss-scss',
    },
    {
      files: ['**/*.(html|vue)'],
      customSyntax: 'postcss-html',
    },
  ],
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts', '**/*.json', '**/*.md', '**/*.yaml'],
  /**
   * null  => 关闭该规则
   * always => 必须
   */
  rules: {
    'value-keyword-case': null, // 在 css 中使用 v-bind，不报错
    'no-descending-specificity': null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    'function-url-quotes': 'always', // 要求或禁止 URL 的引号 "always(必须加上引号)"|"never(没有引号)"
    'no-empty-source': null, // 关闭禁止空源码
    'selector-class-pattern': null, // 关闭强制选择器类名的格式
    'property-no-unknown': null, // 禁止未知的属性(true 为不允许)
    'block-opening-brace-space-before': 'always', //大括号之前必须有一个空格或不能有空白符
    'value-no-vendor-prefix': null, // 关闭 属性值前缀 --webkit-box
    'property-no-vendor-prefix': null, // 关闭 属性前缀 -webkit-mask
    'selector-pseudo-class-no-unknown': [
      // 不允许未知的选择器
      true,
      {
        ignorePseudoClasses: ['global', 'v-deep', 'deep'], // 忽略属性，修改element默认样式的时候能使用到
      },
    ],
  },
}
```

##### .stylelintignore 忽略文件

```bash
/node_modules/*
/dist/*
/html/*
/public/*
```

##### 运行脚本

```json
"scripts": {
	"lint:style": "stylelint src/**/*.{css,scss,vue} --cache --fix"
}
```

最后配置统一的 prettier 来格式化我们的 js 和 css，html 代码

```json
 "scripts": {
    "dev": "vite --open",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint src",
    "fix": "eslint src --fix",
    "format": "prettier --write \"./**/*.{html,vue,ts,js,json,md}\"",
    "lint:eslint": "eslint src/**/*.{ts,vue} --cache --fix",
    "lint:style": "stylelint src/**/*.{css,scss,vue} --cache --fix"
  },
```

**当我们运行`yarn format`的时候，会把代码直接格式化**

#### husky

在上面我们已经集成好了我们代码校验工具，但是需要每次手动的去执行命令才会格式化我们的代码。如果有人没有格式化就提交了远程仓库中，那这个规范就没什么用。所以我们需要强制让开发人员按照代码规范来提交。

要做到这件事情，就需要利用 husky 在代码提交之前触发 git hook(git 在客户端的钩子)，然后执行`yarn format`来自动的格式化我们的代码。

##### 安装 husky

```bash
# 安装husky
yarn add -D husky

# 执行
npx husky-init
```

会在根目录下生成个一个.husky 目录，在这个目录下面会有一个 pre-commit 文件，**这个文件里面的命令在我们执行 commit 的时候就会执行**。

##### 配置 husky

在`.husky/pre-commit`文件添加如下命令：

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
yarn format
```

当我们对代码进行 commit 操作的时候，就会执行命令，对代码进行格式化，然后再提交。

#### commitlint

对于我们的 commit 信息，也是有统一规范的，不能随便写,要让每个人都按照统一的标准来执行，我们可以利用**commitlint**来实现。

##### 安装 commitlint

```bash
yarn add @commitlint/config-conventional @commitlint/cli -D
```

##### commitlint.config.cjs 配置文件

```cjs
module.exports = {
  extends: ['@commitlint/config-conventional'],
  // 校验规则
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'chore', 'revert', 'build'],
    ],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72],
  },
}
```

##### 修改 package.json

在`package.json`中配置 scripts 命令

```json
{
  "scripts": {
    "commitlint": "commitlint --config commitlint.config.cjs -e -V"
  }
}
```

配置结束，现在当我们填写`commit`信息的时候，前面就需要带着下面的`subject`

```m
'feat',//新特性、新功能
'fix',//修改bug
'docs',//文档修改
'style',//代码格式修改, 注意不是 css 修改
'refactor',//代码重构
'perf',//优化相关，比如提升性能、体验
'test',//测试用例修改
'chore',//其他修改, 比如改变构建流程、或者增加依赖库、工具等
'revert',//回滚到上一个版本
'build',//编译相关的修改，例如发布版本、对项目构建或者依赖的改动
```

##### 配置 husky

```bash
npx husky add .husky/commit-msg
```

在生成的 commit-msg 文件中添加下面的命令

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
yarn commitlint
```

当我们 commit 提交信息时，就不能再随意写了，必须是 git commit -m 'fix: xxx' 符合类型的才可以，**需要注意的是类型的后面需要用英文的 :，并且冒号后面是需要空一格的，这个是不能省略的**

#### 强制使用 yarn 包管理器工具

团队开发项目的时候，需要统一包管理器工具,因为不同包管理器工具下载同一个依赖,可能版本不一样,

导致项目出现 bug 问题,因此包管理器工具需要统一管理！！！
在根目录创建`scritps/preinstall.js`文件，添加下面的内容

```js
if (!/yarn/.test(process.env.npm_execpath || '')) {
  console.warn(
    `\u001b[33mThis repository must using yarn as the package manager ` +
      ` for scripts to work properly.\u001b[39m\n`,
  )
  process.exit(1)
}
```

配置命令

```json
"scripts": {
	"preinstall": "node ./scripts/preinstall.js"
}
```

**当我们使用 npm 或者 yarn 来安装包的时候，就会报错了。原理就是在 install 的时候会触发 preinstall（npm 提供的生命周期钩子）这个文件里面的代码。**

### 项目集成

#### 集成 element-plus

硅谷甄选运营平台,UI 组件库采用的 element-plus，因此需要集成 element-plus 插件！！！

官网地址:https://element-plus.gitee.io/zh-CN/

```bash
yarn add element-plus @element-plus/icons-vue
```

**入口文件 main.ts 全局安装 element-plus,element-plus 默认支持语言英语设置为中文**

```ts
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
//@ts-ignore忽略当前文件ts类型的检测否则有红色提示(打包会失败)
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'
app.use(ElementPlus, {
  locale: zhCn,
})
```

**Element Plus 全局组件类型声明**

```json
// tsconfig.json
{
  "compilerOptions": {
    // ...
    "types": ["element-plus/global"]
  }
}
```

配置完毕可以测试 element-plus 组件与图标的使用.

#### src 别名的配置

在开发项目的时候文件与文件关系可能很复杂，因此我们需要给 src 文件夹配置一个别名！！！

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve('./src'), // 相对路径别名配置，使用 @ 代替 src
    },
  },
})
```

**TypeScript 编译配置**

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./", // 解析非相对模块的基地址，默认是当前目录
    "paths": {
      //路径映射，相对于baseUrl
      "@/*": ["src/*"]
    }
  }
}
```

#### 环境变量的配置

**项目开发过程中，至少会经历开发环境、测试环境和生产环境(即正式环境)三个阶段。不同阶段请求的状态(如接口地址等)不尽相同，若手动切换接口地址是相当繁琐且易出错的。于是环境变量配置的需求就应运而生，我们只需做简单的配置，把环境状态切换的工作交给代码。**

开发环境（development）
顾名思义，开发使用的环境，每位开发人员在自己的 dev 分支上干活，开发到一定程度，同事会合并代码，进行联调。

测试环境（testing）
测试同事干活的环境啦，一般会由测试同事自己来部署，然后在此环境进行测试

生产环境（production）
生产环境是指正式提供对外服务的，一般会关掉错误报告，打开错误日志。(正式提供给客户使用的环境。)

注意:一般情况下，一个环境对应一台服务器,也有的公司开发与测试环境是一台服务器！！！

项目根目录分别添加 开发、生产和测试环境的文件!

```bash
.env.development
.env.production
.env.test
```

文件内容

```bash
# .env.development
# 变量必须以 VITE_ 为前缀才能暴露给外部读取
NODE_ENV = 'development'
VITE_APP_TITLE = '硅谷甄选运营平台'
# 开发阶段基础路径变量
VITE_APP_BASE_API = '/dev-api'
# 开发阶段服务器地址变量
VITE_SERVE = 'http://xxxx.com'
```

```bash
# .env.production
# 变量必须以 VITE_ 为前缀才能暴露给外部读取
NODE_ENV = 'production'
VITE_APP_TITLE = '硅谷甄选运营平台'
VITE_APP_BASE_API = '/prod-api'
VITE_SERVE = 'http://production.com'
```

```bash
# .env.test
# 变量必须以 VITE_ 为前缀才能暴露给外部读取
NODE_ENV = 'test'
VITE_APP_TITLE = '硅谷甄选运营平台'
VITE_APP_BASE_API = '/test-api'
VITE_SERVE = 'http://test.com'
```

配置运行命令：package.json

```json
 "scripts": {
    "dev": "vite --open",
    "build:test": "vue-tsc && vite build --mode test",
    "build:pro": "vue-tsc && vite build --mode production",
    "preview": "vite preview"
  },
```

通过 `import.meta.env` 获取环境变量。

#### SVG图标配置

