## 📣 简介
vue-big-table是一个基于vue的简单易理解的大数据量表格插件，完美解决大数据量渲染、滚动卡顿问题，且滚动动态加载数据时是无缝连接的

## 🔰 安装和引入方式
  ** npm方式安装 **
``` javascript
   // npm i vue-big-table -S

  // npm引入方式 如下
  // main.js
  import vueBigTable from 'vue-big-table'

  Vue.use(vueBigTable);

  new Vue({
    el: '#app',
    render: h => h(App)
  });
  //demo.vue
  <vue-big-table :tableList="dataTable" :columns="columns" :tdHeight="60" :tableBodyHeight="600"></vue-big-table>
```

## 📝 API
| 属性 | 说明 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| tableList | 表格数据 | Array | [] |
| columns | 表格列的配置描述 | Array | [] |
| tdHeight | 表格行高 | Number,String | 20 |
| tableBodyHeight | 表格body部分高度 | Number,String | 400 |
