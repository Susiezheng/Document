# 全局引用 vuex 功能

## Install

```bash
$ yarn add vuex
$ yarn add vuex-class
```

## 模块化 Vuex

```js
import Vue from 'vue'; // vue插件
import Vuex from 'vuex';
import modules from './module/index';

Vue.use(Vuex);

const store = new Vuex.Store({
  modules,
});

export default store;
```

### index 文件定义参数名

```js
import login from './login';

export default {
  login,
};
```

### 对于 vuex 做数据处理

1. state 为基本数据变量
1. getters 为从基本数据派生的数据
1. mutations => 提交更改数据的方法，同步！
1. actions => 像一个装饰器，包裹 mutations，使之可以异步

注意 namespaced 为 false 的时候，state,mutations,actions 全局可以调用，为 true，生成作用域，引用时要声明模块名称

```js
import { IndexState } from '@/types/views/index.interface';
import { ActionTree, Commit, MutationTree } from 'vuex';

const initState: IndexState = {
  loading: false,
};

// getters
const getters = {};

// mutations
const mutations: MutationTree<State> = {
  [SET_LOADING](state: State): void {
    state.loading = !state.loading;
  },
};

// actions
const actions: ActionTree<State, any> = {
  async getQueryBookList(
    context: { commit: Commit, state: State },
    params: any
  ) {
    context.commit(SET_LOADING);
  },
};

export default {
  namespaced: true,
  state: initState,
  getters,
  actions,
  mutations,
};
```

### 全局数据调用

这个是 modules 模块化 Vuex 时，已经定义了 login,所以可以直接调用

```js
 import { Action, State } from 'vuex-class';
 @State('login/loading') private loading!: any;
 @Action('login/LOGIN') private LOGIN: any;

```

或者

```js
 import { namespace } from 'vuex-class';
 const someModule = namespace('login');
 @someModule.State((state) => state.loading) public loading: any;
 @someModule.Action('LOGIN') public LOGIN: any;

```

或者

```js
import { mapMutations } from 'vuex'
@Component({
    components: {

    },
    ...mapMutations([ //vuex的dispatch
        'SET_TITLE_NAME'
    ])
}) 

this.$store.commit('SET_TITLE_NAME','ssss');
```
