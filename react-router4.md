# react-router3 升级 4

### 先把原本的包删除，在安装新版本的包

```bash
yarn remove react-router
yarn remove react
 yarn add react-router
 yarn add react
```

### react-router 4 分开了 react-router 和 react-router-dom，原先使用的 react-router 进行跳转路由，全换成 react-router-dom，原先的 query 现在也去除了，采用 search，进行传参数，取值，4 的 json 数据取值、赋值，都使用 queryString 进行解析

### react-router 4 解决了 3 的跳转路由的 BUG，采用 hashHistory 进行跳转路由，会渲染两次 POP\PUSH 类型。4 这个 BUG 已修复

```js
import { Link, hashHistory } from 'react-router-dom';
import queryString from 'query-string';

<Link
  to={{
    pathname: `/a`,
    search: queryString.stringify({ a: 'd' })
  }}
>
  <span>ddd</span>
</Link>;

const { a } = queryString.parse(this.props.location.search);
```

### react-router 4 不采用 router 嵌套写法，IndexRedirect 去掉了，采用 Redirect

#### react-router 3

```js
import { Router, Route, IndexRedirect, IndexRoute, Link, hashHistory } from 'react-router';

const router = (
  <Route path="/" breadcrumbName={i18n.home} component={APP}>
    <IndexRoute
      getComponent={function(nexState, callback) {
        import('./controllers/home').then(m => {
          callback(null, m['Home']);
        });
      }}
    />
    <Route path="UserInfo" breadcrumbName="ff" component={UserMessage} />
  </Route>
);
```

#### react-router 4

```js
import { HashRouter as Router, Route, Switch, Redirect } from 'react-router-dom';

const routes = [
  {
    path: '/UserInfo',
    component: UserMessage,
    loader: UserMessage
  },
  {
    path: '/APPList',
    component: APPList,
    loader: Loadable({
      loader: () => import('./controllers/appList'),
      loading: MyLoadingComponent
    })
  }
];

<Router history={history}>
  <App routes={routes}>
    <Switch>
      {routes.map(({ path, loader, component }, key) => <Route exact key={key} component={component} path={path} />)}
      <Redirect to="/index" />
    </Switch>
  </App>
</Router>;
```
