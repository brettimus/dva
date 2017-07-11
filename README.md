# dva

[![NPM version](https://img.shields.io/npm/v/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Build Status](https://img.shields.io/travis/dvajs/dva.svg?style=flat)](https://travis-ci.org/dvajs/dva)
[![Coverage Status](https://img.shields.io/coveralls/dvajs/dva.svg?style=flat)](https://coveralls.io/r/dvajs/dva)
[![NPM downloads](http://img.shields.io/npm/dm/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Dependencies](https://david-dm.org/dvajs/dva/status.svg)](https://david-dm.org/dvajs/dva)
[![Join the chat at https://gitter.im/dvajs/Lobby](https://img.shields.io/gitter/room/dvajs/Lobby.svg?style=flat)](https://gitter.im/dvajs/Lobby?utm_source=share-link&utm_medium=link&utm_campaign=share-link)

[以中文查看](./README_zh-CN.md)

Lightweight front-end framework based on [redux](https://github.com/reactjs/redux), [redux-saga](https://github.com/yelouafi/redux-saga) and [react-router@2.x](https://github.com/ReactTraining/react-router/tree/v2.8.1). (Inspired by [elm](http://elm-lang.org/) and [choo](https://github.com/yoshuawuyts/choo).)

---

## Features

* **Easy to learn, easy to use**: There are only 6 APIs, and they are very familiar to users of [redux](http://redux.js.org).
* **[Elm](http://elm-lang.org/) concepts**: Models are composed of `reducers`, `effects` and `subscriptions`.
* **Works across platforms**: dva is mobile / React Native friendly. ([Check out the React Native Example](https://github.com/sorrycc/dva-example-react-native).)
* **Supports Hot Module Replacement**: dva supports HMR for components, routes, and models with a babel plugin, [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr).
* **Supports dynamic loading of models and routes**: Send users only the code they need, when they need it. ([Check out the dynamic loading example](https://github.com/dvajs/dva/tree/master/examples/dynamic-load).)
* **A robust plugin system**: E.g., the [dva-loading](https://github.com/dvajs/dva-loading) plugin handles application loading states automatically.
* **TypeScript support**：Bring types to your codebase with `d.ts`. ([See the example](https://github.com/sorrycc/dva-boilerplate-typescript).)

## Why use dva?

* [Why dva and what's dva](https://github.com/dvajs/dva/issues/1) (*In Chinese—please help translate!*)
* [支付宝前端应用架构的发展和选择](https://www.github.com/sorrycc/blog/issues/6) (*In Chinese—please help translate!*)

## Demos

* [Count](https://github.com/dvajs/dva/blob/master/examples/count) ([Demo](https://jsfiddle.net/puftw0ea/3/)): Simple counter 
* [User Dashboard](https://github.com/dvajs/dva-example-user-dashboard): User management dashboard
* [HackerNews](https://github.com/dvajs/dva-hackernews)  ([Demo](https://dvajs.github.io/dva-hackernews/)): HackerNews Clone
* [antd-admin](https://github.com/zuiidea/antd-admin) ([Demo](http://zuiidea.github.io/antd-admin/)): Admin dashboard based on antd and dva
* [github-stars](https://github.com/sorrycc/github-stars) ([Demo](http://sorrycc.github.io/github-stars/#/?_k=rmj86f)): Github Star management tool
* [react-native-dva-starter](https://github.com/nihgwu/react-native-dva-starter): React Native starter powered by dva and [react-navigation](https://reactnavigation.org)

## Quick Start

- [Getting Started](https://github.com/dvajs/dva/blob/master/docs/GettingStarted.md): Build a demo counter app
- [12 步 30 分钟，完成用户管理的 CURD 应用 (react+dva+antd)](https://github.com/sorrycc/blog/issues/18)

## FAQ

### Why is it called dva?

> D.Va’s mech is nimble and powerful — its twin Fusion Cannons blast away with autofire at short range, and she can use its Boosters to barrel over enemies and obstacles, or deflect attacks with her projectile-dismantling Defense Matrix.

—— From [OverWatch](http://ow.blizzard.cn/heroes/dva)

<img src="https://zos.alipayobjects.com/rmsportal/psagSCVHOKQVqqNjjMdf.jpg" width="200" height="200" />

### Is dva production ready?

Sure! We know of over 100 projects that use dva, both inside of Alibaba and out.

### Does dva support IE8?

No.

## How do I learn more about dva?

* Read about the [8 Concepts](https://github.com/dvajs/dva/blob/master/docs/Concepts.md) and how dva connects them together.
* Read the [dva API reference](https://github.com/dvajs/dva/blob/master/docs/API.md).
* Check out the [dva knowledgemap](https://github.com/dvajs/dva-knowledgemap), which includes all the basic knowledge with ES6, React, dva. (*In Chinese—please help translate!*)
* Browse [more FAQ](https://github.com/dvajs/dva/issues?q=is%3Aissue+is%3Aclosed+label%3Afaq)
* Explore creating a project with [dva-cli](https://github.com/dvajs/dva-cli)
* Read about how to [configure dva-cli projects](https://github.com/sorrycc/roadhog/blob/master/README_en-us.md#configuration)

_Do you want even more learning resources? Here you go!_

> All of the following resources are in Chinese, but some do provide excellent code samples.
> Please, if you can provide an English translation for these, open a pull request with links to your translation!

* 看看 dva 的前身 [React + Redux 最佳实践](https://github.com/sorrycc/blog/issues/1)，知道 dva 是怎么来的
* 在 gitc 分享 dva 的 PPT ：[React 应用框架在蚂蚁金服的实践](http://slides.com/sorrycc/dva)
* 如果还在用 dva@0.x，请尽快 [升级到 1.x](https://github.com/dvajs/dva/pull/42#issuecomment-241323617)

## License

[MIT](https://tldrlegal.com/license/mit-license)
