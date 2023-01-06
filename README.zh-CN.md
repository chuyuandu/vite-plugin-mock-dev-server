# vite-plugin-mock-dev-server

<br>
<br>
<p align="center">vite mock开发服务（mock-dev-server）插件。</p>

<p align="center">在 vite 开发环境中，注入一个 mock-dev-server。</p>

<br>
<p align="center">
<a href="https://www.npmjs.com/package/vite-plugin-mock-dev-server"><img alt="npm" src="https://img.shields.io/npm/v/vite-plugin-mock-dev-server?style=flat-square"></a>
<img alt="node-current" src="https://img.shields.io/node/v/vite-plugin-mock-dev-server?style=flat-square">
<img alt="npm peer dependency version" src="https://img.shields.io/npm/dependency-version/vite-plugin-mock-dev-server/peer/vite?style=flat-square">
<img alt="GitHub" src="https://img.shields.io/github/license/pengzhanbo/vite-plugin-mock-dev-server?style=flat-square">
<img alt="npm" src="https://img.shields.io/npm/dm/vite-plugin-mock-dev-server?style=flat-square">
</p>
<br>
<p align="center">
<a href="/README.md">English</a> | <span>简体中文</span>
</p>
<br>
<br>

## 特性

- ⚡️ 轻量，灵活，快速
- 🧲 非注入式，对客户端代码无侵入
- 💡 ESModule/commonjs
- 🦾 Typescript
- 🏷 支持 json / json5 编写 mock 数据
- 📦 自动加载 mock 文件
- 🎨 可选择你喜欢的任意用于生成mock数据库，如 `mockjs`，或者不是用其他库
- 📥 路径规则匹配，请求参数匹配
- ⚙️ 随意开启或关闭对某个接口的 mock配置
- 🔥 热更新
- ⚖️ 使用 `server.proxy` 配置
- 🍕 支持在 mock文件中使用 `viteConfig.define`配置字段
- 📤 支持 multipart 类型，模拟文件上传


## 文档

查看 [Documentation](https://vite-plugin-mock-dev-server.netlify.app/) 了解更多。
[![Netlify Status](https://api.netlify.com/api/v1/badges/9ccda610-2c6a-4cd0-aeaa-a8932f2b477c/deploy-status)](https://app.netlify.com/sites/vite-plugin-mock-dev-server/deploys)
## 使用

### 安装

```sh
# npm
npm i -D vite-plugin-mock-dev-server
# yarn 
yarn add vite-plugin-mock-dev-server
# pnpm
pnpm add -D vite-plugin-mock-dev-server
```

### 配置

`vite.config.ts`
```ts
import { defineConfig } from 'vite'
import mockDevServerPlugin from 'vite-plugin-mock-dev-server'

export default defineConfig({
  plugins: [
    mockDevServerPlugin(),
  ],
  // 这里定义的字段，在mock中也能使用
  define: {},
  server: {
    proxy: {
      '^/api': {
        target: 'http://example.com'
      }
    }
  }
})
```
插件会读取 `server.proxy` 的配置， 仅对设置了代理的 url 匹配，启用mock 匹配。

插件也会读取 `define` 配置， 支持在 mock 文件中直接使用。

> 因为一般场景下，我们只需要对有代理的url进行mock，这样才能通过 vite 提供的 http 服务进行 代理和 mock

### 编写mock文件

默认配置，在你的项目根目录的 `mock` 目录中编写mock数据：

`mock/api.mock.ts` :
```ts
import { defineMock } from 'vite-plugin-mock-dev-server'

export default defineMock({
  url: '/api/test',
  body: {
    a: 1,
    b: 2,
  }
})
```

## 方法

### mockDevServerPlugin(options)

vite plugin


`vite.config.ts`
```ts
import { defineConfig } from 'vite'
import mockDevServerPlugin from 'vite-plugin-mock-dev-server'

export default defineConfig({
  plugins: [
    mockDevServerPlugin(),
  ]
})
```

#### options

- `option.include` 
  
  配置读取 mock文件，可以是一个 目录，glob，或者一个数组

  默认值： `['mock/**/*.mock.{js,ts,cjs,mjs,json,json5}']` (相对于根目录)

- `options.exclude`
  
  配置读取 mock文件时，需要排除的文件， 可以是一个 目录、glob、或者一个数组

  默认值：`['**/node_modules/**','**/test/**','**/cypress/**','src/**','**/.vscode/**','**/.git/**','**/dist/**',]`

- `options.formidableOptions`
  
  配置 `formidable`，查看 [formidable options](https://github.com/node-formidable/formidable#options)

  默认值: `{}`

  示例: 配置文件上传的存放目录
  ```ts
  MockDevServerPlugin({
    formidableOptions: {
      uploadDir: path.join(process.cwd(), 'uploads'),
    }
  })
  ```


### defineMock(config)

mock 配置帮助函数，提供类型检查帮助

```ts
import { defineMock } from 'vite-plugin-mock-dev-server'

export default defineMock({
  url: '/api/test',
  body: {}
})
```

## Mock 配置

```ts
export default defineMock({
  /**
   * 请求地址，支持 `/api/user/:id` 格式 
   */
  url: '/api/test',
  /**
   * 接口支持的请求方法
   * 
   * @type string | string[]
   * @default ['POST','GET']
   * 
   */
  method: ['GET', 'POST'],
  /**
   * 是否启用当前 mock请求
   * 
   * 在实际场景中，我们一般只需要某几个mock接口生效，
   * 而不是所以mock接口都启用。
   * 对当前不需要mock的接口，可设置为 false
   * 
   * @default true
   */
  enable: true,
  /**
   * 设置接口响应延迟， 单位：ms
   * 
   * @default 0
   */
  delay: 1000,
  /**
   * 响应状态码
   * 
   * @default 200
   */
  status: 200,
  /**
   * 响应状态文本
   */
  statusText: 'OK',
  /**
   * 请求验证器，通过验证器则返回 mock数据，否则不是用当前mock。
   * 这对于一些场景中，某个接口需要通过不同的入参来返回不同的数据，
   * 验证器可以很好的解决这一类问题，将同个 url 分为多个 mock配置，
   * 根据 验证器来判断哪个mock配置生效。
   * 
   * @type { header?: object; body?: object; query?: object; params?: object  }
   * 
   * 如果 validator 传入的是一个对象，那么验证方式是严格比较 请求的接口
   * 中，headers/body/query/params 的各个`key`的`value`是否全等，
   * 全等则校验通过
   * 
   * @type ({ header: object; body: object; query: object; params: object }) => boolean
   * 如果 validator 传入的是一个函数，那么会讲 请求的接口相关数据作为入参，提供给使用者进行自定义校验，并返回一个 boolean
   * 
   */
  validator: {
    headers: {},
    body: {},
    query: {},
    params: {},
  },
  /**
   * 
   * 响应状态 headers
   * 
   * @type Record<string, any>
   * 
   * @type (({ query, body, params, headers }) => Record<string, any>)
   * 入参部分为 请求相关信息
   */
  headers: {
    'Content-Type': 'application/json'
  },

  /**
   * 响应体数据
   * 定义返回的响应体数据内容。
   * 在这里，你可以直接返回JavaScript支持的数据类型如 `string/number/array/object` 等
   * 同时，你也可以使用如 `mockjs` 等库来生成数据内容
   * 
   * @type string | number | array | object
   *  直接返回定义的数据
   * 
   * @type (request: { header, query, body, params }) => any | Promise<any>
   * 如果传入一个函数，那么可以更加灵活的定义返回响应体数据
   */
  body: {},

  /**
   * 如果通过 body 配置不能解决mock需求，
   * 那么可以通过 配置 response，暴露http server 的接口，
   * 实现完全可控的自定义配置
   * 
   * 在 req参数中，已内置了 query、body、params 的解析，
   * 你可以直接使用它们
   * 
   * 别忘了，需要通过 `res.end()` 返回响应体数据，
   * 或者需要跳过mock，那么别忘了调用 `next()`
   */
  response(req, res, next) {
    res.end()
  }
})

```

> 注意：
> 
> 如果使用 json/json5 编写 mock文件，则不支持使用 `response` 方法，以及不支持使用其他字段的函数形式。

`mock/**/*.mock.{ts,js,mjs,cjs,json,json5}`

查看更多示例： [example](/example/)

#### 示例1：
命中 `/api/test` 请求，并返回一个 数据为空的响应体内容
```ts
export default defineMock({
  url: '/api/test',
})
```

#### 示例2：
命中 `/api/test` 请求，并返回一个固定内容数据
```ts
export default defineMock({
  url: '/api/test',
  body: {
    a: 1
  }
})
```

#### 示例3：
限定只允许 `GET` 请求
```ts
export default defineMock({
  url: '/api/test',
  method: 'GET'
})
```

#### 示例4：
在返回的响应头中，添加自定义header
```ts
export default defineMock({
  url: '/api/test',
  headers: {
    'X-Custom': '12345678'
  }
})
```
```ts
export default defineMock({
  url: '/api/test',
  headers({ query, body, params, headers }) {
    return {
      'X-Custom': query.custom
    }
  }
})
```

#### 示例5：
定义多个相同url请求mock，并使用验证器匹配生效规则
```ts
export default defineMock([
  // 命中 /api/test?a=1
  {
    url: '/api/test',
    validator: {
      query: {
        a: 1
      }
    },
    body: {
      message: 'query.a === 1'
    }
  },
  // 命中 /api/test?a=2
  {
    url: '/api/test',
    validator: {
      query: {
        a: 2
      }
    },
    body: {
      message: 'query.a === 2'
    }
  }
])
```

#### 示例6：
延迟接口响应：
```ts
export default defineMock({
  url: '/api/test',
  delay: 6000, // 延迟 6秒
})
```

#### 示例7：
使接口请求失败
```ts
export default defineMock({
  url: '/api/test',
  status: 504,
  statusText: 'Bad Gateway'
})
```

#### 示例8：
使用 `mockjs` 生成响应数据:
```ts
import Mock from 'mockjs'
export default defineMock({
  url: '/api/test',
  body: Mock.mock({
    'list|1-10': [{
      'id|+1': 1
    }]
  })
})
```
请先安装 `mockjs`

### 示例9：
使用 `response` 自定义响应
```ts
export default defineMock({
  url: '/api/test',
  response(req, res, next) {
    const { query, body, params, headers } = req
    console.log(query, body, params, headers)

    res.status = 200
    res.setHeader('Content-Type', 'application/json')
    res.end(JSON.stringify({
      query,
      body,
      params,
    }))
  }
})
```

### 示例10：
使用 json / json5
```json
{
  // 支持 comment
  "url": "/api/test",
  "body": {
    "a": 1
  }
}
```

### Example 11:

multipart, 文件上传.

通过 [`formidable`](https://www.npmjs.com/package/formidable#readme) 支持。
``` html
<form action="/api/upload" method="post" enctype="multipart/form-data">
    <p>
      <span>file: </span>
      <input type="file" name="files" multiple />
    </p>
    <p>
      <span>name:</span>
      <input type="text" name="name" value="mark">
    </p>
    <p>
      <input type="submit" value="submit">
    </p>
  </form>
```

fields `files` 映射为 `formidable.File` 类型。
``` ts
export default defineMock({
  url: '/api/upload',
  method: 'POST',
  body(req) {
    const body = req.body
    return {
      name: body.name,
      files: body.files.map((file: any) => file.originalFilename),
    }
  },
})
```

## Archives

[awesome-vite](https://github.com/vitejs/awesome-vite#helpers)

## LICENSE

[GPL-3.0](/LICENSE)
