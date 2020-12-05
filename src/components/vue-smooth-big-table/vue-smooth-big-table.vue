<template>
  <div style="position:relative;height:100%">
    <div class="table-div" :style="{height:`${40 + tableBodyHeight}px`}">
      <div id="topDiv" class="table-top" :style="{right:`${isScroll}px`}">
        <table id="topTable" cellpadding="0" cellspacing="0" border="0" style="width:100%;table-layout:fixed;">
          <tr>
            <td v-for="(top,index) in columns" :key="index" class="top-td" :style="top.width?`width:${top.width}px`:'width:auto'">
              <span>{{top.title?top.title:""}}</span>
            </td>
          </tr>
        </table>
      </div>
      <div id="bottomDiv" class="table-bottom" @scroll="myDebounce" v-show="showTableList.length>0" :style="{height:`${tableBodyHeight}px`}">
        <!--<div :style="{height:`${dataTop}px`}"></div>-->
        <table id="bottomTable" cellpadding="0" cellspacing="0" border="0" style="width:100%;table-layout:fixed;" :style="{height:`${loadNum*tdHeight}px`, marginTop:`${dataTop}px`, marginBottom:`${tableOtherBottom}px`}">
          <tr v-for="(items,indexs) in showTableList" :key="indexs" :style="{'line-height':`${tdHeight - 1}px`}" class="trhover">
            <td v-for="(item,index) in items" :key="index" class="bottom-td" :style="item.width?`width:${item.width}px`:'width:auto'">
              {{ item }}
            </td>
          </tr>
        </table>
        <!--<div :style="{height:`${tableOtherBottom}px`}"></div>-->
      </div>
      <div v-show="showTableList.length==0">
        暂无数据
      </div>
    </div>
    <div class="table-bottom-load" v-show="showLoad" :style="{right:`${isScroll}px`,top:'40px',height:`${tableBodyHeight}px`}">
      <svg class="icon loading " aria-hidden="true">
        <use xlink:href="#icon-jiazai"></use>
      </svg>
      <div class="msg">正在拼命加载</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "vueSmoothBigTable",
  props: {
    tableList: {
      //所有表格数据
      type: Array,
      default() {
        return [];
      }
    },
    columns: {
      //所有表格匹配规则
      type: Array,
      default() {
        return [];
      }
    },
    tdHeight: {
      //表格行高
      type: [Number, String],
      default() {
        return 20;
      }
    },
    tableBodyHeight: {
      //表格高度
      type: [Number, String],
      default() {
        return 400;
      }
    },
  },
  data() {
    return {
      loadNum: 40,
      overNum: 10,
      isScroll: 17,
      showLoad: false,
      showTableList: [], //实际显示的表格数据
      dataTotal: 0, //总数据条数
      dataTop: 0, //渲染数据顶部的高度
      scrollTop: 0, //滚动上下的距离
      scrollHeight: 0, //数据滚动的高度
      handleScroll: null,
      selectIndex: -1, //选择的行
      scrollTimer: null // 滚动防抖函数计时器
    };
  },
  computed: {
    tableOtherBottom() {
      //表格剩余数据底部高度
      return (
        this.dataTotal * this.tdHeight -
        this.dataTop -
        this.loadNum * this.tdHeight
      );
    },
  },
  mounted() {
    this.overNum = Math.ceil(this.tableBodyHeight / this.tdHeight);
    this.loadNum = this.overNum * 3;
  },
  methods: {
    /**
     * @description 自己写的防抖函数
     * @param
     * @return
     */
    myDebounce(e) {
      let bottomScroll = document.getElementById("bottomDiv");
      this.needLoad(bottomScroll);
      // 打开定时器可防抖，提高性能，但滚动过快会出现空白
      /*clearTimeout(this.scrollTimer);
      this.scrollTimer = setTimeout(() => {
        this.scrollProcessing(e);
      }, 300);*/
      this.scrollProcessing(e);
    },
    //是否显示加载中
    needLoad(bottomScroll) {
      if (
        Math.abs(bottomScroll.scrollTop - this.scrollTop) >
        this.tdHeight * this.loadNum
      ) {
        this.showLoad = true; //显示加载中
        //this.scrollTop = bottomScroll.scrollTop;
      }
    },
    //滚动处理
    scrollProcessing(e) {
      const bottomScroll = document.getElementById("bottomDiv");
      const direction = bottomScroll.scrollTop >= this.scrollTop; //滚动方向
      //记录上一次向下滚动的位置
      this.scrollTop = bottomScroll.scrollTop;
      direction ? this.handleScrollBottom() : this.handleScrollTop();
      this.showLoad = false;
    },
    //滚动条向上滚动
    handleScrollTop() {
      let topGap = this.scrollTop - this.dataTop;
      if(topGap <= 0){
        const dataTopNum = Math.floor(this.scrollTop / this.tdHeight); //数据顶部条数
        this.dataProcessing(dataTopNum, "top");
      }
    },
    //滚动条向下滚动
    handleScrollBottom() {
      let topGap = this.scrollTop - this.dataTop;
      if(topGap > (this.overNum * this.tdHeight * 2)){
        const dataTopNum = Math.floor(this.scrollTop / this.tdHeight); //数据顶部条数
        this.dataProcessing(dataTopNum, "bottom");
      }
    },
    //上下滚动时数据处理
    dataProcessing(dataTopNum, type) {
      if (type === "top") {
        this.showTableList.splice(0, this.loadNum); //清空可视区数组
        // 异常判断：dataTopNum > this.overNum为true是正常流程，为false是靠近头部的特殊逻辑处理
        let tableListIndex = dataTopNum > this.overNum ? dataTopNum - this.overNum : 0;

        for (let i = 0, n = tableListIndex; i < this.loadNum; i++) {
          //重新计算可视区数组
          this.showTableList.push(this.tableList[n++]);
        }
        this.dataTop = this.scrollTop - (dataTopNum - tableListIndex) * this.tdHeight;
      } else if (type == "bottom") {
        this.showTableList.splice(0, this.loadNum); //清空可视区数组
        // 异常判断：(dataTopNum - this.overNum) < (this.dataTotal - this.loadNum)为true是正常流程，为false是靠近底部的特殊逻辑处理
        let tableListIndex = (dataTopNum - this.overNum) < (this.dataTotal - this.loadNum) ? (dataTopNum - this.overNum) : (this.dataTotal - this.loadNum);

        for (let i = 0, n = tableListIndex; i < this.loadNum; i++) {
          //重新计算可视区数组
          this.showTableList.push(this.tableList[n++]);
        }
        this.dataTop = this.scrollTop - (dataTopNum - tableListIndex) * this.tdHeight;
      }
      this.showLoad = false;
    },
  },
  watch: {
    tableList: {
      handler(newValue, oldValue) {
        document.getElementById("bottomDiv") &&
          (document.getElementById("bottomDiv").scrollTop = 0);
        document.getElementById("bottomDiv") &&
          (document.getElementById("bottomDiv").scrollLeft = 0);
        this.dataTotal = 0; //总数据条数
        this.dataTop = 0; //渲染数据顶部的高度
        this.scrollTop = 0; //滚动上下的距离
        this.showTableList = [];
        if (newValue.length > 0) {
          this.dataTotal = newValue.length; //获取数据长度
          if (this.dataTotal >= this.loadNum) {
            //判断数据长度是否大于设置的每次渲染长度
            for (var i = 0; i < this.loadNum; i++) {
              let data = newValue[i];
              this.showTableList.push(data);
            }
          } else if (this.dataTotal < this.loadNum) {
            this.loadNum = this.dataTotal;
            for (let i = 0; i < this.dataTotal; i++) {
              let data = newValue[i];
              this.showTableList.push(data);
            }
          }
        }
        if (oldValue) {
          this.scrollProcessing();
        }
      }
    },
    tableBodyHeight(newValue) {
      if (newValue) {
        this.scrollProcessing(); //表格高度改变重新计算
      }
    }
  }
};
</script>
<style  scoped>
@import "./table.css";
</style>
