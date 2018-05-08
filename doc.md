# [ 大杀器 ] 使用 lerna 掌控多个 npm 模块开发 

## lerna 是什么？
> Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm.

lerna 可以在一个 git 仓库中管理多个 npm 包。并且还提供良好的开发体验：
1. 自动解决packages之间的依赖关系, 模块间可以通过模块名相互引用
2. 通过 git 检测文件改动，自动更新模块发布版本号、模块依赖版本号
3. 根据 git 提交记录，快速生成CHANGELOG

## lerna 的使用流程
### 环境准备
1. lerna 依赖 git, 确保你安装了 git。
2. lerna 可以快速发布当前项目的所有模块，如果需要此功能，请先配置好 npm 账号。

### 初始化
1. 全局安装 lerna: `npm i -g lerna` 
2. 创建项目文件夹: `mkdir my-lerna`
3. 进入项目并初始化 lerna: `cd my-lerna && lerna init`
4. 启动 lerna: `lerna bootstrap`
经过上述步骤 `my-lerna` 项目结构如下：
```
lerna-repo/
  packages/
  package.json
  lerna.json
```
- packages: 模块包存放的目录，每个模块包都是 1 个文件夹
- lerna.json: lerna 的配置文件

### 添加模块和发布
项目初始化完成后我们开始体验 lerna：
1. 在 `packages/` 创建模块, 每个模块都要包含 `package.json`
  ```
  lerna-repo/
    packages/
      a/
        package.json
      b/
        package.json
    package.json
    lerna.json
  ```
2. git 提交当前更改，然后执行 `lerna publish`   
3. 通过交互式命令行指定发布版本   
  如果你之前配置了 npm 账号并且发布过程无报错。现在就可以在 npm 上找到刚发布的模块了。


## lerna 的两种模式
lerna 有两种包管理模式, 下面介绍下这两种模式的区别
### Fixed/Locked mode (default)
`Fixed/Locked` 模式有如下特点：
1. `lerna.json` 中的 version 永远和版本最新的子模块保持一致
2. 单个模块（该模块没有被其它模块依赖）代码更新时，该模块和 `lerna.json` 的 version 会更新为最新发布版本
3. 单个模块（该模块被其它模块依赖）代码更新时，`该模块`和`依赖该模块的所有模块`以及 `lerna.json` 的 version 都会更新为最新发布版本   

`Fixed/Locked` 模式的 [demo](https://github.com/soluteli/learn-lerna_fixed)


### Independent mode (--independent)
`Independent mode` 模式开启方式: `lerna init ( -i | --independent )`
该模式有如下特点：
1. 每个模块的版本号独立管理
2. 单个模块（该模块没有被其它模块依赖）代码更新时，只有该模块的版本号才会更新
3. 单个模块（该模块被其它模块依赖）代码更新时，`该模块`和`依赖该模块的所有模块`的版本都会更新，但更新的发布版本需要分别在命令行的交互操作指定   

`Independent` 模式的 [demo](https://github.com/soluteli/learn-lerna_independent)

## 常用命令
### init
> Create a new Lerna repo or upgrade an existing repo to the current version of Lerna.   

在当前文件夹内创建一个 lerna 项目（该文件夹需要是一个 git 仓库）或者更新当前项目的 lerna 版本。

| 命令 | 说明 |
| --- | --- |
| `lerna init` | 以 fixed 模式初始化项目 |
| `lerna init ( -i | --independent )` | 以 independent 模式初始化项目 |

### bootstrap
`bootstrap` 命令执行的操作：
1. 执行每个 package 的 `npm install`
2. 关联存在相互引用的模块
3. 执行每个 bootstraped package 的 `npm run prepublish`
4. 执行每个 bootstraped package 的 `npm run npm run prepare`

| 命令 | 说明 |
| --- | --- |
| `lerna bootstrap ci` | 将 `bootstrap` 过程中的 `npm install` 替换为 `npm ci` |

#### add
| 命令 | 说明 |
| --- | --- |
| `lerna add module-1 --scope=module-2` | Install module-1 to module-2 |
| `Install module-1 to module-2 --dev` | Install module-1 to module-2 in devDependencies |
| `lerna add module-1` | Install module-1 in all modules except module-1 |

### publish
<table>
<tr>
<th>命令</th>
<th>说明</th>
</tr>
<tr>
<td>`lerna publish --conventional-commits`</td>
<td>生成 CHANGELOG（TODO：不能生成 commit 信息）</td>
</tr>
<tr>
<td>`lerna publish --skip-git`</td>
<td>跳过 git 操作</td>
</tr>
<tr>
<td>`lerna publish --skip-npm`</td>
<td>跳过 npm 发布操作</td>
</tr>
<tr>
<td>`lerna publish --force-publish [packages | *]`</td>
<td>gitdiff 的检测，直接更新</td>
</tr>
<tr>
<td>`lerna publish --exact`</td>
<td>将被依赖模块的在各模块依赖列表中改为精确版本：`^x.x.x` -> `x.x.x`</td>
</tr>
<tr>
<td>`lerna publish --npm-tag=next`</td>
<td>发布一个预发布版本, 只有使用`npm i <package>@<tag>` 才会安装该版本</td>
</tr>
<tr>
<td>`lerna publish --cd-version (major | minor | patch | premajor | preminor | prepatch | prerelease)`</td>
<td>直接指定 publish 版本号更新方式</td>
</tr>
</table>


注意：
每次 publish 都是一次 git 提交, 我们可以通过在 `lerna.json` 中添加配置格式化每次的信息：
```js
"command": {
    "publish": {
      "message": "chore(release): publish %s",
      "conventionalCommits": true
    }
  }
```


## TODO
- [ ] `lerna publish --conventional-commits` 生成 commit 格式对应的信息。

## 参考链接
- [lerna Github](https://github.com/lerna)
- [npm ci](https://docs.npmjs.com/cli/ci)
- [lerna管理前端packages的最佳实践](https://juejin.im/post/5a989fb451882555731b88c2#heading-4)
- [React + Storybook + Lerna 构建自己的前端UI组件库](https://juejin.im/post/5a8a905c6fb9a06350151e4c)
- [monorepo 新浪潮 | introduce lerna](https://github.com/pigcan/blog/issues/3)