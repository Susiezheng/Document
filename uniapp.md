# 使用 uniapp 进行第三方登录和支付

## 支付

### 先配置 manifest.json

```json
"sdkConfigs" : {
                "ad" : {},
                "payment" : {
                    "weixin" : {
                        "appid" : "",
                        "UniversalLinks" : ""
                    },
                    "alipay" : {}
                },
                "oauth" : {
                    "weixin" : {
                        "appid" : "",
                        "appsecret" : "",
                        "UniversalLinks" : ""
                    }
                },
                "maps" : {},
                "push" : {
                    "unipush" : {}
                }
            },
```

## 使用 uniapp 的官方文档接口调起第三方支付（最重要的是要配置好 appid 和 appsecret）

```js
uni.requestPayment({
  provider: this.radio,
  orderInfo: obj,
  success: (e) => {},
  fail: (e) => {},
  complete: () => {},
});
```

### 微信登录

```js
uni.login({
  provider: value,
  success: (res) => {
    uni.getUserInfo({
      provider: value,
      success: (infoRes) => {},
      fail(e) {},
    });
  },
  fail: (err) => {},
});
```
