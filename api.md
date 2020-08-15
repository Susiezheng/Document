# 封装统一请求插件

```js
//main.ts

import MinRequest from './utils/api';
Vue.use(MinRequest);

//api.ts
import minRequestApi from '@/api/myApi';

class MinRequest {
  public static install: (Vue: any) => void;
}

// tslint:disable-next-line: only-arrow-functions
MinRequest.install = function(Vue) {
  Vue.mixin({
    beforeCreate() {
      if (minRequestApi) {
        Vue._minRequest = minRequestApi;
      }
    },
  });
  Object.defineProperty(Vue.prototype, '$Api', {
    get() {
      return Vue._minRequest.apis; // 挂载 请求
    },
  });
};

export default MinRequest;

//myApi.ts

import collection from './collection';
import TikTok from './TikTok';

export default {
  apis: Object.assign(TikTok, collection),
};

//collection.ts
import request from '../utils/request';
import qs from 'query-string';

export default {};


```