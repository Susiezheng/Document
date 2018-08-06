# 全局引入 mobx 进行统一状态管理

## Install

```bash
$ yarn add mobx
$ yarn add mobx-react
```

## 统一 router 页面

使用的基本案列

```js
import { configure } from 'mobx';
import { Provider } from 'mobx-react';
import AppList from './AppList';
import NodeList from './NodeList';

const stores = {
  AppList,
  NodeList
};
configure({ enforceActions: true });

ReactDOM.render(<Provider {...stores} />, document.getElementById('root'));
```

## 组件内对于状态的引用

```js
import { inject, observer } from 'mobx-react';

@inject('引用的名称')
@observer
class APPList extends Component {
  //内部对于状态的全局使用
}
```

## 状态的管理

```js
import { observable, action } from 'mobx';

class NodeList {
  @observable nodeList = [];

  @action
  getNodeList(nodeList) {
    //内部方法的逻辑
  }
}

export default new NodeList();
```
