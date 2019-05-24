## install组件
```
npm i date-picker-test-steven
```
## 属性
| 属性  | 类型  |  默认值 | 描述  |
| ------------ | ------------ | ------------ | ------------ |
| **visible.sync**  |  boolean  |  false  |  控制隐藏或者显示datepicker  |
| **startDate**  |  string  |  new date()  |  日历面板默认的时间,例如：2019-03-10  |
| **confirmText**  |  string  |  确定  |  确定按钮文本  |
| **cancelText**  |  string  |  取消  |  取消按钮文本  |

## 方法
| 事件  | 返回  | 描述  |
| ------------ | ------------ | ------------ |
|  **change**  |  function  |  返回年月日(string),例如：2019-03-10 |
|  **cancel**  |  function  |  取消事件 |
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
