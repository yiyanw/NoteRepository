# react 笔记

* 快速初始化一个app

  ```tsx
  import { createRoot } from 'react-dom/client'
  const container = document.getElementById('root')
  const root = createRoot(container!)
  root.render(<App />)
  ```

  * 通过上面的方法将\<App/> 顶级元素绑定到dom节点中





# Ionic 开发

## App initialization

```tsx
  <IonApp>
    <IonReactRouter>
      <IonRouterOutlet>
        <Route path="/home" component={Home} exact={true} />
        <Route exact path="/" render={() => <Redirect to="/home" />} />
      </IonRouterOutlet>
    </IonReactRouter>
  </IonApp>
```

* \<IonApp> 最外层元素

* \<IonReactRouter> 就是react的router，功能基本一样
* \<IonRouterOutlet> 就是层皮，最好写上
* \<Route> 根据path参数进行页面跳转
* \<Redirect> 直接就跳转

## LifeCycle Methods

* ionViewWillEnter **进入**某页面**时**触发(包括从堆栈返回)

* ionViewDidEnter **进入**某页面**后**触发(应该是指加载完？)
* IonViewWillLeave **离开**页面**时**触发

* ionViewDidLeave **离开**页面**后**触发

## Global Config

```tsx
setupConfig({
  rippleEffect: false,
  mode: 'md',
});
```

* 这个可以和isPlatform结合，用于配置不同平台的settings

* https://ionicframework.com/docs/v5/react/config

## progressive web apps

* ionic可以配置service worker。他们会拦截所有发送的请求，如果本地已经缓存，可以直接使用本地资源。提升效率
* https://ionicframework.com/docs/v5/react/pwa

## overlay component

* 弹窗，警告之类的

* https://ionicframework.com/docs/v5/react/overlays

## customized icon

* 需要将图片放在src文件下(子文件夹也可以，但是不能放在src外边)

* <img src="note/image-20220510221203731.png" alt="image-20220510221203731" style="zoom:50%;" />

* 创建icons.css文件写入

  ```css
  .vectorMap {
      content: url("../assets/icons/vector_map.svg");
  }
  ```

* 在variables.css文件中import icons.css变成全局样式

  ```css
  @import 'icons.css'
  ```

* 在文件中调用时，使用class参数

  ```tsx
  <IonIcon class="vectorMap"/>
  ```



# CSS

## 选择器

* element.class 选择当前class中所有的element

## 实现

1. 横向滚动 https://juejin.cn/post/6844904087943643144



# 好用的组件库

[swiper](https://swiperjs.com/demos) 横向滚动组件库，有丰富的设置。

# Redux

* state - 传输的数据
  * Domain State - server 返回的state
  * UI State - 当前组件的state
  * APP State - 全局的state

* action - 一个js obj
  * **必须**包含type属性，通常使用字符串
  * 仅描述了有事情发生，没有描述如何更新state
* reducer 用于接收action的纯函数
  * return的值就是存储的值
* store - 用于联系ation和reducer
  * 主要职责
    * 维护state
    * 获取state - getState()
    * 发送action - dispatch()
    * 注册监听 & 注销监听 - subscribe()

## coding exmaple

* create action | reducer | store directory in root dir

* create index.ts for them -- the purpose is to set the entry for all reducers and actions

  * action index.

    ```typescript
    export const sendAction = {
      type: "send_type", // mandatory vriable
      val1: "this is an action", // variable name and default value.
    };
    ```

  * reducer index.ts

    ```typescript
    export const reducer = (
      state: any = { val1: "default value" },
      action: any
    ) => {
      console.log(`reducer:`, state, action);
      switch (action.type) {
        case "send_type":
          return { ...state, ...action }; // returing means updating state to what returned
        default:
          return state;
      }
    };
    ```

  * store index.ts

    ```typescript
    import {createStore } from "redux";
    import {reducer} from "../reducer";
    
    const store = createStore(reducer);
    
    export default store;
    ```

* use redux in other components

  ```typescript
  import React, { useState } from "react";
  import store from "../../store";
  
  import { sendAction } from "../../action";
  
  export const Home: React.FC = (props) => {
    let [val, setVal] = useState(0);
    const handleClick = () => {
      // send action, this action is update 'val1' to "new Value: val"
      store.dispatch({ ...sendAction, val1: "new Value: " + val });
      setVal((pre) => pre + 1);
    };
    return (
      <>
        <button onClick={handleClick}>click me</button>
        <div>{store.getState().val1}</div> /* use state in store */
        <div>{val}</div>
      </>
    );
  };
  
  ```

  

