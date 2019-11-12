# 引入echarts功能

## Install

```bash
$ yarn add echarts
```

## 图表点击事件

鼠标右键事件，点击click,其他事件值echarts上有说明可以去查看

```js
this.myChart.on('contextmenu', (params) => {
           console.log('右键事件');
           console.log(params);
         });
```

## 但是这时候会发现自带的右键事件也触发，需要关闭
```js
// 删除鼠标自带的右键点击事件
document.oncontextmenu = function () {
  return false;
};
```

## 图表下钻功能

### 主要就是进行事件点击之后把之前的图表销毁，然后重新渲染新的图表

```js
this.initChart(this.myChart, this.option, el);

private initChart(myChart, option) {
  myChart.setOption(option);
  myChart.on('click', (object) => {
    object.event.stop();
    const el: any = this.$el.children[1];
    echarts.dispose(el);
    this.myChart = echarts.init(el);
    this.myChart.setOption(this.option, true);
  });
}
```

## 图表翻转下钻
```js
<div :class="`echarts flip ${cssShow}`" />
```
### 进行点击事件切换cssShow的值为in或者out，来进行点击下钻翻转图表功能

```css

  .in {
    -webkit-animation-timing-function: ease-out;
    -webkit-animation-duration: 1000ms;
    animation-timing-function: ease-out;
    animation-duration: 1000ms;
  }

  .out {
    -webkit-animation-timing-function: ease-in;
    -webkit-animation-duration: 800ms;
    animation-timing-function: ease-in;
    animation-duration: 800ms;
  }

  .flip {
    -webkit-backface-visibility: hidden;
    -webkit-transform: translateX(0); /* Needed to work around an iOS 3.1 bug that causes listview thumbs to disappear when -webkit-visibility:hidden is used. */
    backface-visibility: hidden;
    transform: translateX(0);
  }

  .flip.out {
    -webkit-transform: rotateY(-90deg) scale(.9);
    -webkit-animation-name: flipouttoleft;
    -webkit-animation-duration: 875ms;
    transform: rotateY(-90deg) scale(.9);
    animation-name: flipouttoleft;
    animation-duration: 675ms;
  }

  .flip.in {
    -webkit-animation-name: flipintoright;
    -webkit-animation-duration: 800ms;
    animation-name: flipintoright;
    animation-duration: 800ms;
  }

  @-webkit-keyframes flipouttoleft {
    from {
      -webkit-transform: rotateY(0);
    }
    to {
      -webkit-transform: rotateY(-90deg) scale(.9);
    }
  }

  @keyframes flipouttoleft {
    from {
      transform: rotateY(0);
    }
    to {
      transform: rotateY(-90deg) scale(.9);
    }
  }

  @-webkit-keyframes flipintoright {
    from {
      -webkit-transform: rotateY(90deg) scale(.9);
    }
    to {
      -webkit-transform: rotateY(0);
    }
  }

  @keyframes flipintoright {
    from {
      transform: rotateY(90deg) scale(.9);
    }
    to {
      transform: rotateY(0);
    }
  }
```

