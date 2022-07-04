<template>
  <div class="data_list">
    <header class="header">
      <el-upload
        class="upload-demo"
        :action="actionUrl"
        :limit="1"
        accept=".xls, .xlsx"
        :show-file-list="false"
        :on-success="upOk"
        :on-error="upEr"
        :before-upload="beforeUp"
        :auto-upload="true"
      >
        <el-button size="small" type="primary">数据更新</el-button>
      </el-upload>
    </header>
    <section class="table">
      <div class="table_head">
        <span class="label">选择时间范围：</span>
        <el-date-picker
          v-model="value1"
          type="datetimerange"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          size="mini"
          value-format="yyyy-MM-dd HH:mm:ss"
        >
        </el-date-picker>
        <span class="label label2">渠道ID：</span>
        <el-input
          size="mini"
          class="input_sty"
          v-model="input"
          placeholder="请输入渠道ID"
        ></el-input>
      </div>
      <el-table :data="tableData" class="table_con">
        <el-table-column fixed prop="riqi" label="日期"> </el-table-column>
        <el-table-column prop="name" label="推广产品"> </el-table-column>
        <el-table-column prop="xid" label="渠道ID"> </el-table-column>
        <el-table-column prop="xitong" label="操作系统"> </el-table-column>
        <el-table-column prop="jishuoshu" label="激活数"> </el-table-column>
        <el-table-column prop="danjia" label="预估结算单价"> </el-table-column>
        <el-table-column prop="je" label="预估结算金额"> </el-table-column>
        <el-table-column prop="clv" label="次留率"> </el-table-column>
      </el-table>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      tableData: [],
      value1: "",
      input: "",
      actionUrl: "http://47.94.250.82:8000/upload/one",
      instance: "",
    };
  },
  watch: {
    value1() {
      this.getData();
    },
    input() {
      this.getData();
    },
  },
  mounted() {
    this.getData();
  },
  methods: {
    handleExceed() {
      this.$message.warning("您只能上传单个的表格文件");
    },
    onSuccess() {
      this.$message.success("更新成功！");
      this.getData();
    },
    onError() {
      this.$message.error("上传失败，请重试！");
    },
    upOk(response) {
      console.log(response);
      this.instance.close();
      if (response.code == 200) {
        this.$notify({
          title: "提示",
          message: "数据更新成功！",
          duration: 3000,
        });
        this.getData();
      } else {
        this.$notify({
          title: "提示",
          message: "数据更新失败！",
          duration: 3000,
        });
      }
    },
    upEr(err) {
      console.log(err);
      this.instance.close();
      this.$notify({
        title: "提示",
        message: "数据更新失败！",
        duration: 3000,
      });
    },
    beforeUp() {
      this.instance = this.$notify({
        title: "提示",
        message: "文件正在上传，请勿刷新页面...",
        duration: 0,
      });
    },
    getData() {
      axios
        .get("http://47.94.250.82:8000/upload/find", {
          params: {
            start: this.value1 ? this.value1[0] : undefined,
            end: this.value1 ? this.value1[1] : undefined,
            id: this.input ? this.input : undefined,
          },
        })
        .then((res) => {
          if (res.data.code == 200) {
            this.tableData = res.data.result;
          }
        });
    },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
.header {
  text-align: left;
  padding-left: 20px;
  font-size: 30px;
}
.table {
  padding: 20px;
}
.table_head {
  display: flex;
  align-items: center;
}
.table_head .label {
  padding-right: 20px;
}
.table_head .label2 {
  padding-left: 20px;
}
.input_sty {
  width: 200px;
}
.table_con {
  width: 100%;
  height: calc(100vh - 140px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
  margin-top: 20px;
}
</style>
