# ESLint

# 环境配置

## TypeScript 中使用 ESLint

首先来安装依赖：

```sh
$ yarn add --dev eslint
$ yarn add --dev @typescript-eslint/eslint-plugin
$ yarn add --dev @typescript-eslint/parser
```

然后来对 ESLint 进行配置，在目录下创建 .eslintrc.js 文件：

```js
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: ['plugin:@typescript-eslint/recommended']
};
```

最后，我们还需要在 VSCode 中设置对于 TS 与 TSX 使用 ESLint：

```json
"eslint.validate": [
  "javascript",
  "javascriptreact",
  "typescript",
  "typescriptreact"
]
```

# 链接

- https://mp.weixin.qq.com/s/X2gShxrCw0ukZigjE_45kA
