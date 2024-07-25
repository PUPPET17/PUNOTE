# uniapp

## 踩坑
- ### localStorage
  - uniapp 中使用 localStorage 编译成微信小程序会报错，需要使用uni.setStorage()和uni.getStorage()代替
  - #### uni.setStorage(OBJECT)
    将数据存储在本地缓存中指定的 key 中，会覆盖掉原来该 key 对应的内容，这是一个异步接口。

    | 参数名   | 类型   | 必填 | 说明                                            |
    |----------|--------|------|-------------------------------------------------|
    | key      | String | 是   | 本地缓存中的指定的 key                          |
    | data     | Any    | 是   | 需要存储的内容，只支持原生类型、及能够通过 JSON.stringify 序列化的对象 |
    | success  | Function | 否   | 接口调用成功的回调函数                           |
    | fail     | Function | 否   | 接口调用失败的回调函数                           |
    | complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |

    ```terminal
        uni.setStorage({
          key: 'storage_key',
          data: 'hello',
          success: function () {
            console.log('success');
          }
        });
    ```
  - #### uni.setStorageSync(KEY,DATA)
    将 data 存储在本地缓存中指定的 key 中，会覆盖掉原来该 key 对应的内容，这是一个同步接口。

    | 参数   | 类型   | 必填 | 说明                                                                 |
    |--------|--------|------|----------------------------------------------------------------------|
    | key    | String | 是   | 本地缓存中的指定的 key                                               |
    | data   | Any    | 是   | 需要存储的内容，只支持原生类型、及能够通过 JSON.stringify 序列化的对象 |
    ```terminal
        try {
          uni.setStorageSync('storage_key', 'hello');
        } catch (e) {
          // error
        }
    ```
- ### axios 二次封装
  - 使用 uniapp-axios-adapter
  ```terminal
    >|npm i uniapp-axios-adapter
  ```
  - 修改 axios.js
  ```terminal
    import { UniAdapter } from "uniapp-axios-adapter";
    const service = axios.create({
      baseURL: "http://localhost:8090",
      timeout: 100000, // Request timeout
      adapter: UniAdapter,
    });
  ```
