# Module Seed

> 一个为模块开发集成了常用工作流的种子仓库。

[![CircleCI](https://circleci.com/gh/JerryC8080/module-seed/tree/master.svg?style=svg)](https://circleci.com/gh/JerryC8080/module-seed/tree/master)

[![NPM Version](https://img.shields.io/npm/v/@jerryc/module-seed.svg)](https://www.npmjs.com/package/@jerryc/module-seed) [![NPM Downloads](https://img.shields.io/npm/dm/@jerryc/module-seed.svg)](https://www.npmjs.com/package/@jerryc/module-seed) [![Coverage Status](https://coveralls.io/repos/github/JerryC8080/module-seed/badge.svg?branch=master)](https://coveralls.io/github/JerryC8080/module-seed?branch=master) ![npm bundle size](https://img.shields.io/bundlephobia/minzip/@jerryc/mini-logger.svg)

## Motivation

平时喜欢写一些 NPM 模块，写得多了，整理出一套工作流，解放一些重复的搭建工作。  
如果你喜欢，请直接拿去用，也可以参照该项目的一些 Feature ，给你一些提示与帮助。

## Feature

1. 支持 Typescript
1. 支持单元测试，与测试覆盖率
1. 快速生成文档站点
1. 接入 Circle CLI，构建、 发包、文档站点一条龙服务
1. 规范 ESLint + Prettier
1. 快速生成 Change Log
1. 生成同时支持 CommonJS 和 ES Module 的 NPM 包

## Download

```shell
git clone git@github.com:JerryC8080/module-seed.git
```

## Usage

### 1. Architecture

```shell
.
├── .circleci // CircleCI 脚本
│   ├── config.yml
├── coverage // 自动生成的测试覆盖率报告
├── docs  // 自动生成的文档
├── build  // 构建代码
│   ├── main  // 兼容 CommonJS
│   │   ├── index.d.ts
│   │   ├── index.js
│   │   └── lib
│   └── module  // 兼容 ES Module
│       ├── index.d.ts
│       ├── index.js
│       └── lib
├── src  // 源码
│   ├── index.ts
│   └── lib
│       ├── hello-world.spec.ts // 单元测试
│       └── hello-world.ts
├── CHANGELOG.md
├── LICENSE
├── README.md
├── package.json
├── tsconfig.json
└── tsconfig.module.json
```

### 2. Npm Script

| Script      | Description                                         |
| ----------- | --------------------------------------------------- |
| build       | 构建代码，生成 ./build 文件夹                       |
| fix         | 快速格式化代码                                      |
| test        | 构建单元测试                                        |
| watch:build |  动态构建代码，用于开发模式                         |
| watch:test  | 动态构建单元测试，用于开发模式                      |
| cov         | 构建单元测试覆盖率，生成 ./coverage 文件夹          |
| doc         | 构建文档站点，生成 ./docs 文件夹                    |
| doc:publish | 发布文档站点到 github pages                         |
| version     |  强制以 patch 模式更新 version，如：v0.0.1 → v0.0.2 |

### 3. Coverage

通过运行 `npm run cov`，命令会构建单元测试，并且输出网页版本的测试报告：

```shell
open coverage/index.html
```

![coverage](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212173414.png)

### 4. Docs

通过运行 `npm run doc`，会构建 TS API 文档，并且输出网页版本：

```shell
open docs/index.html
```

![docs](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212173820.png)

### 5. CircleCI Config

本项目选择 [CircleCI](https://circleci.com/) 来做项目的 CI，完成项目构建、发布 NPM、发布文档站点等工作。

#### 1. Add Repo to CircleCI

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212174309.png)

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212174310.png)

#### 2. Test Coverage to Coveralls

如果想拥有一个这样的 Status: ![Coverage Status](https://coveralls.io/repos/github/JerryC8080/module-seed/badge.svg?branch=master)

需要把你的 repo 添加到 [coveralls.io](https://coveralls.io/)

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212175434.png)

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212175435.png)

然后，在 CircleCI 添加环境变量 `COVERALLS_REPO_TOKEN`

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212175703.png)

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212175850.png)

那么，每次 CircleCI 发生构建的时候，就会上报单元测试覆盖率到 coveralls 去。

参考 `.circleci/config.yml` 相关脚本:

```yml
...
upload-coveralls:
  <<: *defaults
  steps:
    - attach_workspace:
        at: ~/repo
    - run:
        name: Test Coverage
        command: npm run cov
    - run:
        name: Report for coveralls
        command: |
          npm install coveralls --save-dev
          ./node_modules/.bin/nyc report --reporter=text-lcov | ./node_modules/.bin/coveralls
    - store_artifacts:
        path: coverage
...
```

#### 3. Doc Site to Github Pages

本地可以通过命令来构建和发布文档站点到 Github Pages

```shell
npm run doc
npm run doc:publish
```

如果这个动作交给 CircleCI 来完成，则需要为 Repo 添加一个 `Read/Write` 权限的 `User Key`

![](https://bluesun-1252625244.cos.ap-guangzhou.myqcloud.com/img/20201212180750.png)

那么，每次 CircleCI 发生构建的时候，就会构建文档，并发布到  github pages 中去。

例如本项目，就可以通过以下地址访问：

[https://jerryc8080.github.io/module-seed](https://jerryc8080.github.io/module-seed)

参考 `.circleci/config.yml` 相关脚本:

```yml
...
 deploy-docs:
    <<: *defaults
    steps:
      - attach_workspace:
          at: ~/repo
      - run:
          name: Avoid hosts unknown for github
          command: mkdir ~/.ssh/ && echo -e "Host github.com\n\tStrictHostKeyChecking no\n" > ~/.ssh/config
      - run:
          name: Set github email and user
          command: |
            git config --global user.email "huangjerryc@gmail.com"
            git config --global user.name "CircleCI-Robot"
      - run:
          name: Show coverage
          command: ls coverage
      - run:
          name: Show docs
          command: ls docs
      - run:
          name: Copy to docs folder
          command: |
            mkdir docs/coverage
            cp -rf coverage/* docs/coverage
      - run:
          name: Show docs
          command: ls docs
      - run:
          name: Publish to gh-pages
          command: npm run doc:publish
...
```

#### 4. Coverage site

在 CircleCI 的 `deploy-docs` 任务中，会构建 Coverage Site ，然后一起发布到 Github Pages 的 `/coverage` 目录中。

例如本项目，就可以通过以下地址访问：

[https://jerryc8080.github.io/module-seed/coverage/index.html](https://jerryc8080.github.io/module-seed/coverage/index.html)

#### 5. NPM Deploy

## License

This project is licensed under the [MIT license](LICENSE).  
Copyright (c) JerryC Huang (huangjerryc@gmail.com)
