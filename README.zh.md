<p align="center">
  <img alt="Lerna" src="https://cloud.githubusercontent.com/assets/952783/15271604/6da94f96-1a06-11e6-8b04-dc3171f79a90.png" width="480">
</p>

<p align="center">
  A tool for managing JavaScript projects with multiple packages.
  一个针对含有多个 package 的 JavaScript 项目管理工具
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/lerna"><img alt="NPM Status" src="https://img.shields.io/npm/v/lerna.svg?style=flat"></a>
  <a href="https://travis-ci.org/lerna/lerna"><img alt="Travis Status" src="https://img.shields.io/travis/lerna/lerna/master.svg?style=flat&label=travis"></a>
</p>

- [About](#about)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Troubleshooting](#troubleshooting)
- Commands
  - [`lerna publish`](./commands/publish#readme)
  - [`lerna version`](./commands/version#readme)
  - [`lerna bootstrap`](./commands/bootstrap#readme)
  - [`lerna list`](./commands/list#readme)
  - [`lerna changed`](./commands/changed#readme)
  - [`lerna diff`](./commands/diff#readme)
  - [`lerna exec`](./commands/exec#readme)
  - [`lerna run`](./commands/run#readme)
  - [`lerna init`](./commands/init#readme)
  - [`lerna add`](./commands/add#readme)
  - [`lerna clean`](./commands/clean#readme)
  - [`lerna import`](./commands/import#readme)
  - [`lerna link`](./commands/link#readme)
  - [`lerna create`](./commands/create#readme)
  - [`lerna info`](./commands/info#readme)
- [Concepts](#concepts)
- [Lerna.json](#lernajson)
- [Global Flags](./core/global-options)
- [Filter Flags](./core/filter-options)

## About

把大型的代码分割成独立版本包对代码共享（复用）是非常有效的。但是，跨越多个仓库去修改代码会很混乱而且难以追踪，并且测试时很困难的。

为了解决这些问题，一些项目会将他们的代码仓库分成不同的 package 仓库(叫做 [monorepos](https://github.com/babel/babel/blob/master/doc/design/monorepo.md)). 像 [Babel](https://github.com/babel/babel/tree/master/packages), [React](https://github.com/facebook/react/tree/master/packages), [Angular](https://github.com/angular/angular/tree/master/modules),
[Ember](https://github.com/emberjs/ember.js/tree/master/packages), [Meteor](https://github.com/meteor/meteor/tree/devel/packages), [Jest](https://github.com/facebook/jest/tree/master/packages) 这样的和一些其他的项目都是再同一个仓库下开发他们的所有的 package

**Lerna 是一个使用 git 和 npm 来优化多包管理的工作流的工具.**

在开发和构建环境中，Lerna 还可以有多个相同包副本的情况下减少大量时间和空间的需求 - 通常下是将项目分为许多单独 NPM 包的缺点. 这里有更详尽的信息
[hoist documentation](doc/hoist.md).

### Lerna repo 长啥样子

这个结构很微小. 文件结构的样子如下:

```
my-lerna-repo/
  package.json
  packages/
    package-1/
      package.json
    package-2/
      package.json
```

### Lerna 做了啥?

Lerna 的两个主要的命命令是 `lerna bootstrap` 和 `lerna publish`.

`bootstrap` 用来链接包
`publish` 用来发布和更改包

### Lerna 不能做啥?

Lerna 不是用来给 serverless monorepos 部署的工具。 Hoistring 可能与传统的 serverless monorepo部署技术不兼容。

## Getting Started

> 下面的介绍是基于 Lerna 3.x.
> 我们推荐用它来替代 2.x 或更早的 Lerna 版本

让我们用 [npm](https://www.npmjs.com/) 安装 Lerna 依赖开始你的项目

```sh
$ mkdir lerna-repo && cd $_
$ npx lerna init
```

以上将会创建一个 `lerna.json` 配置文件和一个 `packages` 文件夹，这样你的文件目录结构就像这样

```
lerna-repo/
  packages/
  package.json
  lerna.json
```

## 怎样工作的

Lerna 允许你用这两个模式管理你的项目: Fixed 或者 Independent。

### Fixed/Locked mode (default)

Fixed 模式中， Lerna 项目操作唯一一行版本号。版本号在根目录里的 `lerna.json` 文件中 `version` 字段。当你运行 `lerna publish` 的时候，如果一个模块与上一次 release 版本相比已经被修改，版本号将修改为你发布的版本号。这意味着你仅需要的时候才发布包的新版本。

这个模式 [Babel](https://github.com/babel/babel) 正在用。如果你想绑定所有模块到一个版本号，你可以用这个模式。这种模式有一个问题就是，任意一个包有一个重大更改，都会导致所有的包都有一个新的主要版本号。

### Independent mode

`lerna init --independent`

独立模式的 Lerna 项目允许项目维护者彼此独立地增加 package 的版本。当你每次发布的时候，Lerna 会针对每一个修改过的 package 进行提示提示，是 patch , minor, major 还是 custom change。

独立模式使您可以更具体地更新每个软件包的版本，并使一组组件有意义。通过与这种模式结合，就像 [semantic-release](https://github.com/semantic-release/semantic-release) 将会减少痛苦。(有的工作早已经基于此来进行的了 [atlassian/lerna-semantic-release](https://github.com/atlassian/lerna-semantic-release)).

> 在 `lerna.json` 里的 `version` 字段设置为 `independent` 来运行为独立模式

## 故障排除

如果你在用 Lerna 的时候遇到任何问题，请查阅我们的  [Troubleshooting](doc/troubleshooting.md) 文档，也许你能找到问题的答案。

## 如果你老有问题

看这里 [FAQ.md](FAQ.md).

## 概念

Lerna 如果遇到错误的时候，错误日志会打到 `lerna-debug.log` 文件 （就像 `npm-debug.log`) 

Lerna 同时也支持 [scoped packages](https://docs.npmjs.com/misc/scope).

运行 `lerna --help` 来看我们到底提供啥命令

### lerna.json

```json
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": ["ignored-file", "*.md"],
      "message": "chore(release): publish",
      "registry": "https://npm.pkg.github.com"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```

- `version`: 当前仓库版本.
- `npmClient`: 指定运行命令的客户端。（也可以是一个基础命令). 改成 `yarn` 可以用 yarn 运行所有的命令. 默认是 "npm".
- `command.publish.ignoreChanges`:  一个数组，里面的内容将不会被 `lerna changed/publish` 包含. 利用这个可以避免无用文件的改变导致产生新的版本的情况发生，比如修复 `README.md` 的拼写错误什么的.
- `command.publish.message`: 当执行发布版本更新时，可以自定义提交信息。[@lerna/version](commands/version#--message-msg) 这里有更多信息.
- `command.publish.registry`: 自定义仓库地址来替代 npmjs.org. 如果需要鉴权的话，需要提前鉴权过.
- `command.bootstrap.ignore`: 一坨数组，配置的内容不会被 `lerna bootstrap` 命令执行.
- `command.bootstrap.npmClientArgs`: 一坨数组，当执行 `lerna bootstrap` 的时候，这里配置的内容将直接透传给 `npm install`.
- `command.bootstrap.scope`: 一坨数组，当执行 `lerna bootstrap` 的时候，这里配置的内容将指定哪些包将被引导(bootstrapped).
- `packages`: 一坨数组，指定包的目录位置.

`learn.json` 里面的包的配置是与包含 `package.json` 的目录匹配的列表，是 lerna 用来组织 "leaf"(叶子) 包的方式 （与 "root" `package.json` 比较，"root" `package.json` 的目的是为整个的 repo 管理开发依赖和脚本。

默认情况下， lerna 的初始化包列表是 `["packages/*"]`, 但是你也可以使用别的目录比如 `["modules/*"]`, 或 `["package1", "package2"]`。这一坨定义的目录路径应该是在 `learn.json` 文件所在位置的相对路径，这常用来作为仓库的根目录。唯一的限制就是你不能直接指定包的位置，但是这个限制在 "normal(通常)" 的 npm 包也存在。（我没理解这个意思，对不起）

比如 `["packages/*", "src/**"]` 对应的树形结构:

```
packages/
├── foo-pkg
│   └── package.json
├── bar-pkg
│   └── package.json
├── baz-pkg
│   └── package.json
└── qux-pkg
    └── package.json
src/
├── admin
│   ├── my-app
│   │   └── package.json
│   ├── stuff
│   │   └── package.json
│   └── things
│       └── package.json
├── profile
│   └── more-things
│       └── package.json
├── property
│   ├── more-stuff
│   │   └── package.json
│   └── other-things
│       └── package.json
└── upload
    └── other-stuff
        └── package.json
```

叶子包的位置是在 `packages/*` 下是通常意义上的一个 "最佳实践"，但是对于 lerna 它不是必须的

#### 遗留字段

一些 `lerna.json` 字段不再使用。需要注意的地方如下：

* `lerna`: 最初用于指示Lerna的当前版本。 [Made obsolete](https://github.com/lerna/lerna/pull/1122) 和 [removed](https://github.com/lerna/lerna/pull/1225) in v3

### Common `devDependencies`

大多数的 `devDependencies` 可以通过 `lerna link convert` 拉取到 Lerna repo 的根目录

上面的命令会自动将依赖提升，使用 `file:` 的相对路径格式

The above command will automatically hoist things and use relative `file:` specifiers.

吊起有以下一些收益：
- 所有的包可以使用同一个版本的依赖
- 可以保证依赖在根目录通过自动化工具保持最新，比如 [GreenKeeper](https://greenkeeper.io/)
- 减少了依赖安装时间
- 减少了存储需要的空间

需要注意的是 `devDependencies` 提供的被 "npm scripts"使用的可执行二进制文件，依然需要安装到各自包的里。

举个例子，`nsp` 依赖是正确运行 `lerna run nsp` 所必须的（`npm run nsp` 需要运行在包的路径）: 


```json
{
  "scripts": {
    "nsp": "nsp"
  },
  "devDependencies": {
    "nsp": "^2.3.3"
  }
}
```

### Git Hosted Dependencies

Lerna 允许本地依赖包的目标版本写作 [git remote url](https://docs.npmjs.com/cli/install)  一样，通过 `cimmittish` （比如 `#v1.0.0` 或者 `#semver:^1.0.0`） 替代正常的数字范围版本。

当包是私有的或者是 [private npm registry is not desired](https://www.dotconferences.com/2016/05/fabien-potencier-monolithic-repositories-vs-many-repositories) 的时候，通过这样的方式可以把包通过Git仓库组织起来。

需要注意 lerna 不会 将 git 历史实际拆分为单独的只读存储库。这是用户的责任。([this comment](https://github.com/lerna/lerna/pull/1033#issuecomment-335894690) 看更多的细节) （译者：没看懂）


```
// packages/pkg-1/package.json
{
  name: "pkg-1",
  version: "1.0.0",
  dependencies: {
    "pkg-2": "github:example-user/pkg-2#v1.0.0"
  }
}

// packages/pkg-2/package.json
{
  name: "pkg-2",
  version: "1.0.0"
}
```

在上面的例子中

- `lerna bootstrap` 将 `pkg-2` 正确的链接到 `pkg-1`
- `lerna publish` 当 `pkg-2` 修改后， 此命令会将 `pkg-2` 的 committish (`#v1.0.0`) 更改为最新版本

### README Badge

Using Lerna? Add a README badge to show it off: [![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)

```
[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)
```

### 向导

如果你想要更多关于 cli 的文档 （比如你要开始使用 leran 或 将他介绍给团队），你也许会喜欢 [lerna-wizard](https://github.com/szarouski/lerna-wizard)。里面有一些最佳实践来指导你

![lerna-wizard demo image](https://raw.githubusercontent.com/szarouski/lerna-wizard/2e269fb5a3af7100397a1f874cea3fa78089486e/demo.png)