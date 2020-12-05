<template>
  <div id="app">
    <vue-smooth-big-table :tableList="dataTable" :columns="columns" :tdHeight="60" :tableBodyHeight="600"></vue-smooth-big-table>
  </div>
</template>

<script>
import vueSmoothBigTable from './components/vue-smooth-big-table'

export default {
  name: 'app',
  components: {
    vueSmoothBigTable
  },
  data(){
    return {
      columns: [
        //表头
        {
          title: "列1",
          key: "columns1",
          width: null,
          align: "center",
        },
        {
          title: "列2",
          key: "columns2",
          width: null,
          align: "center"
        },
        {
          title: "列3",
          key: "columns3",
          width: null,
          align: "center"
        },
        {
          title: "列4",
          key: "columns4",
          width: null,
          align: "center"
        },
        {
          title: "列5",
          key: "columns5",
          width: null,
          align: "center"
        },
        {
          title: "列6",
          key: "columns6",
          width: null,
          align: "center"
        },
        {
          title: "列7",
          key: "columns7",
          width: null,
          align: "center"
        }
      ],
      dataTable: [] //表格数据
    }
  },
  mounted() {
    for (let i = 0; i < 200000; i++) {
      let data = {};
      for (let m = 0; m < this.columns.length; m++) {
        data[`columns${m}`] = i; //`${i+1}行${m+1}列`;
      }
      this.dataTable.push(data);
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
