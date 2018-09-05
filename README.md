# react项目构建及配置(create-react-app)

## 一、**命令行依次输入如下的命令** 

```
npm install -g create-react-app
create-react-app my-app

cd my-app
npm start
```

创建完成后文件的结构如下：

 ![img](https://images-cdn.shimo.im/fl3lYIPljbMFwXTq/image.png!thumbnail) 



## **二、public文件**

public/favicon.ico           

html的ico 图标，在index.html中会有引入；

可以使用图片，通过线上ico图标制作出，

.ico图标来替换react默认的public/manifest.json      

app配置文件，设置icons，首页路径等； 



## 三、**src文件夹** 

src/index.js 是入口文件

src/App.js是最大的组件App

src/registerserviceworker.js 用来做资源离线的缓存，

注意：registerServiceWorker注册的service worker 只在生产环境(正式环境)中生效。 



## 四、**四个命令** 

① npm start 启动项目，在 <http://localhost:3000>查看项目运行 



② npm run build 项目打包，生成一个build文件夹，如下 

![img](https://images-cdn.shimo.im/0rM65DV2o4MSpnoS/image.png!thumbnail) 



③ npm run eject 弹出配置文件--不可逆操作--用于修改默认的配置

当对react-script配置好的构建工具和配置项不满意时使用这个命令，他将命令运行的脚本js分离出来，输入命令时指向的是这些脚本，你可以根据情况调整这些代码。 



④ npm test --测试 



## 五、**antd-mobile/antd 配置** 

### 1、安装antd/antd-mobile 

```
npm install antd --save
//或者
npm install antd-mobile --save
```



### 2、引入 [react-app-rewired](https://github.com/timarney/react-app-rewired) 并修改 package.json 里的启动配置: 

```
npm install react-app-rewired --save-dev
```

```
/* package.json */
"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test --env=jsdom",
+   "test": "react-app-rewired test --env=jsdom",
}
```



### 3、然后在项目根目录创建一个 config-overrides.js 用于修改默认配置。

```
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config;
};
```

###  

### 4、安装 babel-plugin-import 

 [babel-plugin-import](https://github.com/ant-design/babel-plugin-import) 是一个用于按需加载组件代码和样式的 babel 插件（[原理](https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)），现在我们尝试安装它并修改 config-overrides.js 文件。 

```
npm install babel-plugin-import --save-dev
```

```
+ const { injectBabelPlugin } = require('react-app-rewired');
  module.exports = function override(config, env) {
+   config = injectBabelPlugin(['import', { libraryName: 'antd-mobile', style: 'css' }], config);
    return config;
  };
```



### 5、组件内引用 

```
- import Button from 'antd-mobile/lib/button';
+ import { Button } from 'antd-mobile';
```



## 六、**less 配置** 

### 1.安装所需less依赖 

```
npm install less-loader less --save-dev
```



### 2.安装react-app-rewire-less 

```
npm install --save-dev react-app-rewire-less
```



### 3.根目录下找到刚才的config-overrides.js 继续修改配置 

```
/* config-overrides.js */
const { injectBabelPlugin } = require('react-app-rewired');
+ const rewireLess = require('react-app-rewire-less');

module.exports = function override(config, env) {
    config = injectBabelPlugin(['import', { libraryName: 'antd', libraryDirectory: 'es', style: 'css' }], config);
    + config = rewireLess.withLoaderOptions({modifyVars: { "@primary-color": "#1DA57A" }})(config, env);
    return config;
};
```



## 七、**antd-mobile/antd 配置（弹出内建配置）** 

### 1.弹出配置文件 

```
npm run eject
```

弹出的配置文件如下，弹出了config文件夹和scripts文件夹

config文件夹----中的配置文件可供修改

scripts文件夹----作为命令对应要运行的文件 

![img](https://images-cdn.shimo.im/SB0P0bSCpuAJ7agy/image.png!thumbnail) 

弹出后注意修改package.json 里启动命令的配置 

```
"scripts": {
    "start": "node scripts/start.js",
    "build": "node scripts/build.js",
    "test": "node scripts/test.js --env=jsdom"
  },
```



### 2.安装antd/antd-mobile 

```
npm install antd --save
//或者
npm install antd-mobile --save
```



### 3.安装 babel-plugin-import 

```
npm install babel-plugin-import --save-dev
```



### 4.修改package.json文件 

```
"babel": {
  "presets": [
    "react-app"
  ],
+  "plugins": [
+     [
+       "import",
+      {
+         "libraryName": "antd",
+         "style": "css"
+       }
+     ]
+  ],
},
```



或者根目录下新建一个.babelrc，

写入如下的代码，这个文件将把package.json的babel的配置对象完全覆盖掉 

```
{
   "presets": [
      "react-app"
    ],
    "plugins": [
      [
        "import",
        {
          "libraryName": "antd",
          "style": "css"
        }
      ]
    ]
}
```



## 八、**less 配置（弹出内建配置）** 

### 1.安装所需less依赖 

```
npm install less-loader less --save-dev
```



### 2.修改配置文件 

找到根目录下的config文件夹下的webpack.config.dev.js 和 webpack.config-prod.js

exclude的数组中/\.css$/ 改为 /\.(css|less)$/

 /\.(css|less)$/下的use数组中增加less-loader 

```
use: [
  {
    loader: require.resolve('css-loader'),
    options: {
      importLoaders: 1,
      minimize: true,
      sourceMap: shouldUseSourceMap,
    },
  },
  {
    loader: require.resolve('postcss-loader'),
    options: {
      // Necessary for external CSS imports to work
      // https://github.com/facebookincubator/create-react-app/issues/2677
      ident: 'postcss',
      plugins: () => [
        require('postcss-flexbugs-fixes'),
        autoprefixer({
          browsers: [
            '>1%',
            'last 4 versions',
            'Firefox ESR',
            'not ie < 9', // React doesn't support IE8 anyway
          ],
          flexbox: 'no-2009',
        }),
      ],
    },
  },
+ {
+     loader: require.resolve('less-loader') // compiles Less to CSS
+ }
],
```

