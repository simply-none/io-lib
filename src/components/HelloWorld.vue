<template>
  <div>
    <h-w :data="data" rowKey="name">
      <el-table-column prop="date" label="日期" width="180"> </el-table-column>
      <el-table-column prop="name" label="姓名" width="180"> </el-table-column>
      <el-table-column prop="address" label="地址"> </el-table-column>
    </h-w>
  </div>
</template>

<script>
import HW from "./HelloWorld copy";
export default {
  data() {
    return {
      data: [
        {
          date: "2016-05-02",
          name: "王小虎1",
          address: "上海市普陀区金沙江路 1518 弄",
          children: [
            {
              date: "2016-05-02",
              name: "王小2虎",
              address: "上海市普陀区金沙江路 1518 弄",
            },
            {
              date: "2016-05-02",
              name: "王小12虎",
              address: "上海市普陀区金沙江路 1518 弄",
            },{
              date: "2016-05-02",
              name: "王小虎21",
              address: "上海市普陀区金沙江路 1518 弄",
            },{
              date: "2016-05-02",
              name: "王小32虎",
              address: "上海市普陀区金沙江路 1518 弄",
            },{
              date: "2016-05-02",
              name: "王小32`1虎",
              address: "上海市普陀区金沙江路 1518 弄",
            },{
              date: "2016-05-02",
              name: "王小323虎",
              address: "上海市普陀区金沙江路 1518 弄",
            }
          ]
        },
        {
          date: "2016-05-04",
          name: "王小32121虎",
          address: "上海市普陀区金沙江路 1517 弄",
        },
        {
          date: "2016-05-01",
          name: "王小2131虎",
          address: "上海市普陀区金沙江路 1519 弄",
        },
        {
          date: "2016-05-03",
          name: "王小45虎",
          address: "上海市普陀区金沙江路 1516 弄",
        },
      ],
    };
  },
  components: {
    HW,
  },
};
</script>

<style>
</style>