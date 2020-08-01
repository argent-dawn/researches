# 状态管理

目录：
*   [引言](#引言)
*   [Vuex](#Vuex)
*   [系统状态树](#系统状态树)
    *   [Modules](#Modules)
    *   [代码分离](#代码分离)
    *   [预定义节点](#预定义节点)
    *   [中心 Store](#中心-Store)
*   [单向数据流](#单向数据流)
* 	[Vuex 实现规范](#Vuex-实现规范)
	*   [系统状态树](#系统状态树)
	*   [Module](#Module)
    *   [mutation type](#mutation-type)
    *   [mutations](#mutations)
    *   [actions](#actions)
    *   [getters](#getters)

## 引言
**在组件化开发的基础上，对于一个前端系统，我们将各组件包含的属性和数据统称为系统的 “状态（state）”，其实际展示的内容统称为系统的 “视图（view）”。此时，可以把系统整体理解为一个从状态到视图的映射，即：**

***view = f(state)***

其中 *state* 代表系统的状态，包括各组件对应的属性和数据；*view* 代表系统的视图，常见形式包括 DOM 元素片段，CSS 样式和其他静态资源等；在映射法则 *f* 的作用下，系统的任意一个状态都将唯一对应一个视图。

系统视图的形式并不仅限于在浏览器端运行的网页，取决于映射法则的具体实现。例如当采用 [React Native](#https://facebook.github.io/react-native/) 实现映射法则时，系统视图将会是 Object C 或者 Java 组件。因此在这个模型里，系统视图是没有平台限制的 —— 这种关注分离有助于我们更好地理解和开发跨平台应用。

## Vuex
一个实际的前端系统可能包含数十甚至上百个状态，直接对它们进行操作是非常复杂和混乱的；同时我们需要通过对状态的读取和修改来实现所有业务逻辑，因此有必要引入一套机制来统一管理这些状态。基于目前的组件化框架，我们采用 [Vue](https://cn.vuejs.org/) 官方提供的类 [Flux](https://facebook.github.io/flux/) 状态管理方案 [Vuex](https://vuex.vuejs.org/zh-cn/)。

> [Flux](https://facebook.github.io/flux/) 是 Facebook 专门针对复杂 Web 应用开发所提出的系统架构，社区再此基础上实现了很多改进后的版本，例如 [Redux](#http://redux.js.org/) 是当前 [React](#https://facebook.github.io/react/) 生态环境中最流行的 [Flux](https://facebook.github.io/flux/) 实现。

完整的 [Vuex](https://vuex.vuejs.org/zh-cn/) 方案需要实现以下内容：

*   系统状态树
*   单向数据流

## 系统状态树
系统状态树负责保存完整的系统状态并实现相关业务逻辑，因此除 `state` 外，还必须包括与之对应的 `action`、`mutation type`、`mutation` 和 `getter`，完整的系统状态树在开发时将以中心 Store 的形式实现。

当 state 数量较多时，系统状态树的实现可能会变得非常臃肿。对前端系统而言，页面是逻辑和代码最自然的边界，但由于单页应用（SPA）只有一个页面文件，因此实际开发中我们将根据系统路由（视图组件）来对状态树进行拆分。

系统状态树的设计步骤和规则如下：
*   将系统路由以树形结构（路由树）表示；
*   每个路由树节点唯一对应一个系统状态树节点；
*   路由树的叶子节点代表实际页面，对应的系统状态树节点保存该页面的专有状态；
*   路由树的分支节点不代表实际页面，对应的系统状态树节点保存其子节点（可能是叶子节点或下一级分支节点）间的共享状态；
*   系统状态树采用扁平化结构，所有节点位于同一层级；
*   系统状态树各节点以对应路由作为该节点的唯一标识；

例如一个具备如下路由的单页应用：
```
/                       # 系统根路径
/login                  # /login 路径
/home/main              # /home/main 路径
/home/detail            # /home/detail 路径
```

首先将系统路由以树形结构（路由树）表示为：
```
├─── /                          # 系统根路径
│    ├─── /login                # /login 路径
│    ├─── /home                 # /home 路径
│    │    ├─── /main            # /home/main 路径
│    │    └─── /detail          # /home/detail 路径
```

按照上述规则，对应的系统状态树结构可按 js 对象形式表示为：
```javascript
{
    '$app': { ... },             // 对应路由树根节点，保存系统全局状态

    'login': { ... },            // 对应路由树叶子节点，保存 login 页的专有状态

    'home': { ... },             // 对应路由树分支节点，保存其下属页面的共享状态

    'home/main': { ... },        // 对应路由树叶子节点，保存 home/main 页的专有状态

    'home/detail': {... }        // 对应路由树叶子节点，保存 home/detail 页的专有状态
}
```

### Modules
我们采用模块化开发的方式，将系统状态树各节点对应的 `state`、`action`、`mutation type`、`mutation` 和 `getter` 整合在一个独立的模块（module）中实现。为使代码结构更加直观，每个模块唯一对应一个工程目录和 js 文件，且模块命名和工程目录结构也与系统状态树保持一致。

如上例中，系统状态树实现后的工程目录结构应为：
```
├─── /store
│    ├─── /modules
│    │    ├─── /$app
│    │    │    └─── index.js
│    │    │
│    │    ├─── /login
│    │    │    └─── index.js
│    │    │
│    │    ├─── /home
│    │    │    └─── index.js
│    │    │
│    │    ├─── /home-main
│    │    │    └─── index.js
│    │    │
│    │    └─── /home-detail
│    │         └─── index.js
│    │
│    └─── index.js
```

通常一个完整的 module 需包含以下内容：
*   `state`：以对象形式保存该节点对应的所有状态和初始值；
*   `mutation type`：用于唯一确定一个 `mutation` 的类型；
*   `mutations`：以对象形式保存各类 `mutation` 的响应函数；
*   `actions`：以对象形式保存各类 `mutation` 的触发函数；
*   `getters`：以对象形式保存各 `state` 对应的计算函数；

以 `home/main` 节点对应 module 为例，其完整实现如下：
```javascript
// state
const state = {
    userId: 0
};

// mutation type
const HOME_MAIN_SET_USER_ID = 'HOME_MAIN_SET_USER_ID';

// mutations
const mutations = {
    [HOME_MAIN_SET_USER_ID](state, mutation) {
        state.userId = mutation.payload;
    }
};

// actions
const actions = {
    async homeMainSetUserId({ commit }, userId) {
        commit({
            type: HOME_MAIN_SET_USER_ID,
            payload: userId
        });
    }
};

// getters
const getters = {
    homeMainUserId(state) {
        return state.userId;
    }
};

export default {
    state,
    mutations,
    actions,
    getters
};
```

为保证代码一致性以及避免在状态树合并的过程中产生冲突，我们为 module 制定了一些特殊的开发规范，具体细节请参考：[状态管理规范](http://git.weiresearch.com/wfe/guides/blob/master/guides/state-management/README.md)。

### 代码分离
基于状态管理和组件化方案，我们可以在代码层面将系统分为逻辑层和展示层两部分：
*   逻辑层：由系统状态树实现，负责处理所有业务逻辑；
*   展示层：由视图组件和 UI 组件实现，专注于数据展示；

这种代码分离有助于降低系统耦合度，允许开发团队最大程度并行开发。当发生需求变更时，也易于将代码改动控制在最小范围。

### 预定义节点
在实际开发中，我们针对系统常见需求提供了一组预定义的状态树节点。这些预定义节点与普通节点一样，各自分别对应一个独立 module 进行实现，需要注意的是，所有预定义节点的命名均以 `$` 开头。

目前提供的预定义节点包括：
*   **$group**  
    该节点用于保存不同系统间的共享数据。某些大型系统在实现时可能需要按功能拆分为若干子系统，每个子系统分别对应一个系统状态树。当不同系统间存在需要共享的状态时（例如用户信息和登录状态），应统一将其保存在该节点中。

    $group 的 module 在实现时默认开启了缓存机制，针对其内部状态的任何读写操作都将自动记录在本地缓存，以此来达到跨页面共享状态的目的。
*   **$app**  
    该节点用于保存系统全局状态，对应路由树根节点。
*   **$apis**  
    该节点用于保存系统内各接口的请求参数、调用状态和响应结果，并提供统一封装的 ajax 请求方法（以 action 形式实现），可支持重复请求自动终断、本地数据缓存和数据范式化处理。

    通常情况下，系统内所有 ajax 请求都应通过该 module 提供的 action 方法发送。
*   **$entities**  
    为优化存储空间和查询性能，我们将根据实际需求对接口返回的响应数据进行范式化处理，该节点用于统一保存范式化后的实体数据，以便于在系统范围内全局共享。

    通常情况下，页面的其他节点只保存相关实体主键，在需要时通过调用该节点对应 module 的 getter 方法获取完整数据信息。

因此，上例中完整的系统状态树结构可按 js 对象形式表示为：
```javascript
{
    '$group': { ... },           // 保存不同系统间的共享数据

    '$apis': { ... },            // 保存系统内各接口的请求参数、调用状态和响应结果

    '$entities': { ... },        // 保存范式化后的实体数据

    '$app': { ... },             // 对应路由树根节点，保存系统全局状态

    'login': { ... },            // 对应路由树叶子节点，保存 login 页的专有状态

    'home': { ... },             // 对应路由树分支节点，保存其下属页面的共享状态

    'home/main': { ... },        // 对应路由树叶子节点，保存 home/main 页的专有状态

    'home/detail': {... }        // 对应路由树叶子节点，保存 home/detail 页的专有状态
}
```

### 中心 Store
中心 Store 负责统一加载和管理所有相关 module，并将它们合并成完整的系统状态树。

对于上面例子中的系统，其中心 Store 的工程目录结构如下：
```
├─── /store
│    ├─── /modules          # 状态树各节点对应的 module
│    │    └─── ...
│    └─── index.js          # 中心 Store
```

其中 index.js 的具体实现如下：
```javascript
// https://github.com/vuejs/vuex/issues/451
import 'core-js/fn/promise';
import Vue from 'vue';
import Vuex from 'vuex';
import createPersistedState from 'vuex-persistedstate';
import $group from 'common/utils/$store/modules/$group';
import $apis from 'common/utils/$store/modules/$apis';
import $entities from 'common/utils/$store/modules/$entities';
import $app from './modules/$app';
import login from './modules/login';
import home from './modules/home';
import homeMain from './modules/home-main';
import homeDetail from './modules/home-detail';



Vue.use(Vuex);

export default new Vuex.Store({
    strict: process.env.NODE_ENV !== 'production',
    plugins: [
        createPersistedState({
            key: '$group',
            paths: ['$group']
        })
    ],
    modules: {
        $group,
        $apis,
        $entities,
        $app,

        login,
        home,
        'home/main': homeMain,
        'home/detail': homeDetail
    }
});
```

## 单向数据流
单向数据流机制可以有效降低系统复杂度，并极大地简化代码调试过程。为了实现该机制，在实际开发中各组件应当：
*   尽量将业务逻辑放在系统状态树中实现；
*   通过加载 module 获取指定的状态树节点；
*   不直接修改系统状态，而是通过调用 action 要求中心 Store 执行指定的业务逻辑来更新 state，以响应用户操作；
*   通过调用 getter 从中心 Store 获取对应的 state 来更新视图；

## Vuex 实现规范 

### 系统状态树
*   系统状态树作为工具组件以中心 Store 的形式实现，相关代码统一保存在项目的 `/src/components/utils/store` 路径下；
*   系统状态树采用扁平化结构，所有节点位于同一层级；
*   系统状态树各节点以对应路由作为该节点的唯一标识；

例如：
```javascript
{
   '$app': { ... },
   'login': { ... },
   'home': { ... },
   'home/main': { ... },
   'home/detail': {... }
}
```

### Module
*   module 相关代码统一保存在项目的 `/src/components/utils/store/modules` 路径下；
*   每个 module 唯一对应一个工程目录和 js 文件；
*   module 的命名和工程目录结构必须与系统状态树保持一致；

对于上例中的状态树，其 module 的工程目录结构和命名应为：
```
├─── /store
│    ├─── /modules
│    │    ├─── /$app
│    │    │    └─── index.js
│    │    ├─── /login
│    │    │    └─── index.js
│    │    ├─── /home
│    │    │    └─── index.js
│    │    ├─── /home-main
│    │    │    └─── index.js
│    │    └─── /home-detail
│    │         └─── index.js
│    └─── index.js
```

### mutation type
*   以常量形式实现，且常量名和常量值应保持一致
*   所有字母全部大写，不同单词间以下划线 `_` 分隔
*   命名符合 *节点标识 + 动作 + 状态* 结构
*   *动作* 必须为动词，且能够准确描述 mutation 对应的行为
*   *状态* 必须与 `state` 中对应状态的命名保持一致

例如：
```javascript
const HOME_MAIN_SET_USER_ID = 'HOME_MAIN_SET_USER_ID';
```

### mutations
*   每个 `mutation type` 唯一对应一个响应函数
*   直接使用 `mutation type` 常量作为函数名（通过 ES6 属性名表达式）

例如：
```javascript
const mutations = {
    [HOME_MAIN_SET_USER_ID](state, mutation) {
        state.userId = mutation.payload;
    }
};
```

### actions
*   每个 `mutation type` 至少对应一个触发函数
*   命名符合 *节点标识 + 动作 + 状态* 结构，采用 camelCase 拼写方式
*   *动作* 必须与 `mutation type` 中对应动作的命名保持一致
*   *状态* 必须与 `mutation type` 中对应状态的命名保持一致
*   函数必须采用 ES7 Async 语法，用于实现链式调用
*   生成的 `mutation` 对象必须符合 [Flux Standard Action](https://github.com/acdlite/flux-standard-action) 规范

例如：
```javascript
const actions = {
    async homeMainSetUserId({ commit }, userId) {
        if (util.isNumber(userId)) {
            commit({
                type: HOME_MAIN_SET_USER_ID,
                payload: userId
            });
        } else {
            throw new Error('invalid parameter');
        }
    }
};
```

### getters
*   每个 `mutation type` 至少对应一个计算函数
*   命名符合 *节点标识 + 状态* 结构，采用 camelCase 拼写方式
*   *状态* 必须与 `mutation type` 中对应状态的命名保持一致

例如：
```javascript
const getters = {
    homeMainUserId(state) {
        return state.userId;
    }
};
```
