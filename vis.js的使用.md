# 基于vis.js的使用

## Install

Install via npm:

```bash
$ npm install vis
```
Install via bower:

```bash
$ bower install vis
```


同时也可去GitHub上查看源码:

[https://github.com/almende/vis.git](https://github.com/almende/vis.git).  
<br />
### 预览
![演示结果](http://atlassian.yfb.sunline.cn:8090/download/attachments/13639645/2.gif?version=1&amp;modificationDate=1522823456000&amp;api=v2)

## Example

使用的基本案列

```js
import { DataSet, Network } from 'vis/index-network';
import 'vis/dist/vis-network.min.css';

// create an array with nodes
var nodes = new DataSet([
  {id: 1, label: 'Node 1'},
  {id: 2, label: 'Node 2'},
  {id: 3, label: 'Node 3'},
  {id: 4, label: 'Node 4'},
  {id: 5, label: 'Node 5'}
]);

// create an array with edges
var edges = new DataSet([
  {from: 1, to: 3},
  {from: 1, to: 2},
  {from: 2, to: 4},
  {from: 2, to: 5},
  {from: 3, to: 3}
]);

// create a network
var container = document.getElementById('mynetwork');
var data = {
  nodes: nodes, //数据显示
  edges: edges  //点到点的线数据
};
var options = {
  locales: {
    en: {
          edit: 'Edit',
          del: 'Delete selected',
          //等等，可以进行多语言
        }
  },
  edges: {
    arrows: {
          to: { enabled: true, scaleFactor: 1, type: 'arrow' } //箭头的显示
        },
        color: '#e6fffb',
        font: '16px arial #13c2c2',
        shadow: true // 阴影
  },
  layout: {
        // hierarchical: true  是否显示方式为树形排列，和默认排列
      },
 nodes: {
 	//这里设置为节点的设置
        fixed: false,  
        font: '18px arial #e6fffb',
        scaling: {
          label: true
        },
        shadow: true,
        shape: 'dot',
        font: {
          size: 16
        }
      },
 manipulation: {
        enabled: true, //是否启用编辑
        initiallyActive: false,
        addNode: (data, callback) => {
        //点击新增节点的事件
        },
        addEdge: (data, callback) => {
          //点击连线的事件
        },
        editNode: (data, callback) => {
         //编辑点击的事件
        },
        editEdge: (data, callback) => {
        //编辑连线的事件
          console.log(data);
          callback(data);
        },
        deleteNode: (data, callback) => {
        //删除节点的事件
          console.log(data);
          callback(data);
        },
        deleteEdge: (data, callback) => {
        //删除连线的事件
          console.log(data);
          callback(data);
        },
      },
};
var network = new Network(container, data, options);

render() {
    return (
        <div style={{ height: height, paddingBottom: '25' }} id="netWork" />
    );
  }
  
```

## Example 2: 对于节点的本身事件

```js
//title是鼠标悬停节点时的显示信息
var nodes = new vis.DataSet([
        {id: 1, label: 'Node 1', title: 'I have a popup!'},
        {id: 2, label: 'Node 2', title: 'I have a popup!'},
        {id: 3, label: 'Node 3', title: 'I have a popup!'},
        {id: 4, label: 'Node 4', title: 'I have a popup!'},
        {id: 5, label: 'Node 5', title: 'I have a popup!'}
    ]);
    // create an array with edges
    var edges = new vis.DataSet([
        {from: 1, to: 3},
        {from: 1, to: 2},
        {from: 2, to: 4},
        {from: 2, to: 5}
    ]);
    // create a network
    var container = document.getElementById('mynetwork');
    var data = {
        nodes: nodes,
        edges: edges
    };
    var options = {
			interaction:{hover:true},
		};
    var network = new vis.Network(container, data, options);
    
    
    //点击事件
    network.on('click', function(params) {
      console.log('returns: ' + this.getNodeAt(params.pointer.DOM));
    });
    //双击事件
    network.on('doubleClick', function(params) {
    });
    //右键点击事件
    network.on('oncontext', function(params) {
    });
	//鼠标悬停显示title事件
    network.on('showPopup', function(params) {
    });
    //鼠标显示title移出事件
    network.on('hidePopup', function() {
      console.log('hidePopup Event');
    });
    
```


更多事件参考:
[http://visjs.org/docs/network/](http://visjs.org/docs/network/).


<br />
新增后保存的数据格式,其他所需字段，自定义即可
```json
{
	id: "6d1c595d-385d-4589-a63a-c2d6cdaba746",
	x: -230.23540624105567,
	y: -121.16225112886605,
	label: "新增的节点名称",
}
```
新增连线后数据，点到点之间的ID
```json
{
	from: -1880199183,
	to: -644697307
}
```