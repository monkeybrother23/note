## 环境搭建

### registry

```shell
临时
--registry https://registry.npm.taobao.org
全局
npm config set registry https://registry.npm.taobao.org
```

### list

```shell
npm list -g --depth 0
```

### uninstall

```shell
npm uninstall -g create-react-app 
npm cache clean --force
npm cache verify
npx clear-npx-cache以清除npx缓存。
```

### yarn

```shell
yarn cache clean
yarn start
```

### rimraf

```
npm install rimraf -g
rimraf node_modules
```



### create

```shell
yarn config set registry https://registry.npm.taobao.org -g
yarn create react-app react-demo
yarn add antd
```



