## install组件
```
npm i vue-mobile-datepicker
```
## 预览效果
![enter image description here](https://github.com/zuorong/vue-mobile-datepicker/blob/master/src/assets/preview.jpg?raw=true)
## 属性
| 属性  | 类型  |  默认值 | 描述  |
| ------------ | ------------ | ------------ | ------------ |
| **visible.sync**  |  boolean  |  false  |  控制隐藏或者显示datepicker  |
| **startDate.sync**  |  string  |  new date()  |  日历面板默认的时间,例如：2019-03-10  |
| **confirmText**  |  string  |  确定  |  确定按钮文本  |
| **cancelText**  |  string  |  取消  |  取消按钮文本  |

## 方法
| 事件  | 返回  | 描述  |
| ------------ | ------------ | ------------ |
|  **change**  |  function  |  返回年月日(string),例如：2019-03-10 |
|  **cancel**  |  function  |  取消事件 |
## 使用方法
```
# 全局注册组件
import datepicker from 'vue-mobile-datepicker'
Vue.component('Datepicker', datepicker)
```
```
# 局部注册组件
import Datepicker from 'vue-mobile-datepicker'
export default {
  components: { Datepicker }
}
```
## example运行
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
