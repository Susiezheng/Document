# Object.defineProperty 实现双向绑定

### 在 new SelfVue 后会调用 init 函数初始化，在这个过程中 DATA 通过 observer 转换成 getter 和 setter 的形式对数据追踪变化，当追踪对象被读取时执行 getter,被赋值时执行 setter。当外界通过 watcher 读取数据时，会触发 getter 从而将 watcher 添加到依赖中。当修改对象的值，会触发对应的 setter。setter 会通知之前依赖收集到的 Dep 中的每一个 watcher，告诉它我被改变了，需要重新渲染页面，这时候 watcher 会开始调用 update 来更新视图。

```html
<body>
  <div id="app">
    <div id="name">{{oldName}}</div>
    <h2>{{title}}</h2>
    <input v-model="name" />
    <h1>{{name}}</h1>
    <button v-on:click="clickMe">click me!</button>
  </div>
</body>
<script type="text/javascript">
  function Dep() {
    this.sub = [];
  }
  Dep.prototype = {
    addSub: function (sub) {
      this.sub.push(sub);
    },
    updateSub: function () {
      this.sub.forEach((e) => {
        e.update();
      });
    },
  };
  Dep.target = null;

  function objectSetGet(data, key, value) {
    observer(value);
    let dep = new Dep();
    Object.defineProperty(data, key, {
      get: function () {
        if (Dep.target) {
          // 判断是否需要添加订阅者
          dep.addSub(Dep.target); // 在这里添加一个订阅者
        }
        return value;
      },
      set: function (val) {
        if (val === value) {
          return;
        }

        console.log(val, 'val');

        value = val;
        dep.updateSub();
      },
    });
  }

  function observer(data) {
    if (!data || typeof data !== 'object') {
      return;
    }
    Object.keys(data).forEach((i) => {
      objectSetGet(data, i, data[i]);
    });
  }

  function Watcher(vm, exp, cb) {
    this.vm = vm;
    this.exp = exp;
    this.cb = cb;
    this.value = this.get();
  }

  Watcher.prototype = {
    get: function () {
      Dep.target = this;
      let value = this.vm.data[this.exp];
      Dep.target = null;
      return value;
    },
    update: function () {
      this.run();
    },
    run: function () {
      let value = this.vm.data[this.exp];
      let oldvalue = this.value;
      if (value !== oldvalue) {
        this.value = value;
        this.cb.call(this.vm, value, oldvalue);
      }
    },
  };

  let objS = {
    a: {
      aa: 'aaa',
    },
    b: 'bbb',
    c: 'ccc',
  };

  function self(data, el, exp) {
    let _this = this;
    this.data = data;
    Object.keys(data).forEach((e) => {
      _this.objKey(e);
    });
    observer(data);
    el.innerHTML = this.data[exp];
    new Watcher(this, exp, function (value) {
      el.innerHTML = value;
    });
    return this;
  }

  function SelfVue(options) {
    var self = this;
    this.data = options.data;
    this.methods = options.methods;

    Object.keys(this.data).forEach(function (key) {
      self.objKey(key);
    });

    observer(this.data);
    new Compile(options.el, this);
    options.mounted.call(this); // 所有事情处理好后执行mounted函数
  }

  SelfVue.prototype = {
    objKey: function (key) {
      let _this = this;
      Object.defineProperty(this, key, {
        get: function () {
          return _this.data[key];
        },
        set: function (newKey) {
          _this.data[key] = newKey;
        },
      });
    },
  };
  self.prototype = {
    objKey: function (key) {
      let _this = this;
      Object.defineProperty(this, key, {
        get: function () {
          return _this.data[key];
        },
        set: function (newKey) {
          _this.data[key] = newKey;
        },
      });
    },
  };

  function Compile(el, vm) {
    this.vm = vm;
    this.el = document.querySelector(el);
    this.fragment = null;
    this.init();
  }

  Compile.prototype = {
    init: function () {
      if (this.el) {
        this.fragment = this.nodeToFragment(this.el);
        this.compileElement(this.fragment);
        this.el.appendChild(this.fragment);
      } else {
        console.log('Dom元素不存在');
      }
    },
    nodeToFragment: function (el) {
      var fragment = document.createDocumentFragment();
      var child = el.firstChild;
      while (child) {
        // 将Dom元素移入fragment中
        fragment.appendChild(child);
        child = el.firstChild;
      }
      return fragment;
    },
    compileElement: function (el) {
      var childNodes = el.childNodes;
      var self = this;
      [].slice.call(childNodes).forEach(function (node) {
        var reg = /\{\{(.*)\}\}/;
        var text = node.textContent;

        if (self.isElementNode(node)) {
          self.compile(node);
        } else if (self.isTextNode(node) && reg.test(text)) {
          self.compileText(node, reg.exec(text)[1]);
        }

        if (node.childNodes && node.childNodes.length) {
          self.compileElement(node);
        }
      });
    },
    compile: function (node) {
      var nodeAttrs = node.attributes;
      var self = this;
      Array.prototype.forEach.call(nodeAttrs, function (attr) {
        var attrName = attr.name;
        if (self.isDirective(attrName)) {
          var exp = attr.value;
          var dir = attrName.substring(2);
          if (self.isEventDirective(dir)) {
            // 事件指令
            self.compileEvent(node, self.vm, exp, dir);
          } else {
            // v-model 指令
            self.compileModel(node, self.vm, exp, dir);
          }
          node.removeAttribute(attrName);
        }
      });
    },
    compileText: function (node, exp) {
      var self = this;
      var initText = this.vm[exp];
      this.updateText(node, initText);
      new Watcher(this.vm, exp, function (value) {
        self.updateText(node, value);
      });
    },
    compileEvent: function (node, vm, exp, dir) {
      var eventType = dir.split(':')[1];
      var cb = vm.methods && vm.methods[exp];

      if (eventType && cb) {
        node.addEventListener(eventType, cb.bind(vm), false);
      }
    },
    compileModel: function (node, vm, exp, dir) {
      var self = this;
      var val = this.vm[exp];
      this.modelUpdater(node, val);
      new Watcher(this.vm, exp, function (value) {
        self.modelUpdater(node, value);
      });

      node.addEventListener('input', function (e) {
        var newValue = e.target.value;
        if (val === newValue) {
          return;
        }
        self.vm[exp] = newValue;
        val = newValue;
      });
    },
    updateText: function (node, value) {
      node.textContent = typeof value == 'undefined' ? '' : value;
    },
    modelUpdater: function (node, value, oldValue) {
      node.value = typeof value == 'undefined' ? '' : value;
    },
    isDirective: function (attr) {
      return attr.indexOf('v-') == 0;
    },
    isEventDirective: function (dir) {
      return dir.indexOf('on:') === 0;
    },
    isElementNode: function (node) {
      return node.nodeType == 1;
    },
    isTextNode: function (node) {
      return node.nodeType == 3;
    },
  };

  observer(objS);
  objS.b = '新的bbbb';
  objS.a.aa = 'sssssssss';
  console.log(objS);

  var ele = document.querySelector('#name');
  var selfVues = new self(
    {
      name: 'hello world',
    },
    ele,
    'name'
  );

  window.setTimeout(function () {
    console.log('name值改变了');
    selfVues.name = 'canfoo';
  }, 2000);

  new SelfVue({
    el: '#app',
    data: {
      title: 'hello world',
      name: 'zsq',
    },
    methods: {
      clickMe: function () {
        this.title = 'hello world';
      },
    },
    mounted: function () {
      window.setTimeout(() => {
        this.title = '你好';
      }, 1000);
    },
  });
</script>
```
