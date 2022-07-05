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
      <div class="export_btn">
        <el-button
          size="small"
          type="primary"
          icon="el-icon-download"
          @click="exportData()"
          >数据下载</el-button
        >
      </div>
    </header>
    <section class="table">
      <div class="table_head">
        <span class="label">时间范围：</span>
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
        <span class="label label2">操作系统：</span>
        <el-select
          v-model="caozuoxitong"
          size="mini"
          clearable
          placeholder="请选择操作系统"
        >
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          >
          </el-option>
        </el-select>
        <span class="label label2">推广产品：</span>
        <el-select
          v-model="tgcp"
          size="mini"
          clearable
          placeholder="请选择推广产品"
        >
          <el-option
            v-for="item in tgcpoptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          >
          </el-option>
        </el-select>
      </div>
      <el-table
        :data="tableData"
        class="table_con"
        stripe
        border
        :height="theight"
      >
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
      theight: 500,
      tgcp: "",
      caozuoxitong: "",
      options: [
        {
          label: "全部",
          value: "a",
        },
      ],
      tgcpoptions: [
        {
          label: "全部",
          value: "a",
        },
      ],
    };
  },
  watch: {
    value1() {
      this.getData();
    },
    input() {
      this.getData();
    },
    tgcp() {
      this.getData();
    },
    caozuoxitong() {
      this.getData();
    },
  },
  beforeMount() {
    this.getoptions();
    this.gettgcpoptions();
  },
  mounted() {
    this.$nextTick(() => {
      let h = window.screen.availHeight;
      this.theight = h - 140;
    });
    this.getData();
  },
  methods: {
    exportData() {
      window.open("http://47.94.250.82:8000/static/导出数据.xls", "_blank");
    },
    getoptions() {
      axios.get("http://47.94.250.82:8000/upload/xitong", {}).then((res) => {
        res.data.result.map((el) => {
          let obj = {};
          obj.label = el.xitong;
          obj.value = el.xitong;
          this.options.push(obj);
        });
      });
    },
    gettgcpoptions() {
      axios.get("http://47.94.250.82:8000/upload/chanpin", {}).then((res) => {
        res.data.result.map((el) => {
          let obj = {};
          obj.label = el.name;
          obj.value = el.name;
          this.tgcpoptions.push(obj);
        });
      });
    },
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
            chanpin: this.tgcp ? this.tgcp : undefined,
            xitong: this.caozuoxitong ? this.caozuoxitong : undefined,
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
  display: flex;
}
.export_btn {
  margin-left: 10px;
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
  max-height: calc(100vh - 140px);
  overflow: auto;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
  margin-top: 20px;
}
</style>
