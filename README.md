# lerna 帮你管多个 package 

## lerna 是什么？
> Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm.

lerna 可以在一个 git 仓库中管理多个 npm 包。并且还提供良好的开发体验：
1. 自动解决packages之间的依赖关系, 模块间可以通过模块名相互引用
2. 通过 git 检测文件改动，自动更新模块发布版本号、模块依赖版本号
3. 根据 git 提交记录，自动生成CHANGELOG

## lerna 的两种模式
### Fixed/Locked mode (default)

# learn-lerna_fixed
