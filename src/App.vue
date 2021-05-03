<template>
  <el-container>
    <el-aside width="250px" style="height: 100vh">
      <div>
        <div class="step-card">
          <div>初始投资</div>
          <div class="text-sm margin-top">投资建议</div>
          <div class="text-bold">买入1%</div>
        </div>
        <div class="step-card-choose margin-top">
          <div>第1次投资</div>
          <div class="text-sm margin-top">投资建议</div>
          <div class="text-bold">等待计算中</div>
        </div>
      </div>
    </el-aside>
    <el-main>
      <el-row type="flex" justify="center" align="middle" style="height: 100vh">
        <el-col :xs="22" :sm="20" :md="18" :lg="16" :xl="14">
          <el-row>
            <el-col>
              <div class="margin-top-lg">
                <el-input
                  placeholder="请输入本轮的涨跌幅，例如：-0.15（-15%）"
                  v-model="input2"
                >
                  <template v-slot:append>提交计算</template>
                </el-input>
              </div>
            </el-col>
          </el-row>
          <!-- 仓位价值 -->
          <el-row>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">涨跌幅</div>
              <div class="text-lg text-bold">10%</div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">总价值</div>
              <div class="text-lg text-bold">10000</div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">累计本金</div>
              <div class="text-lg text-bold">10000</div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">净收益</div>
              <div class="text-lg text-bold">100</div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">可买金额</div>
              <div class="text-lg text-bold">5000</div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">可卖金额</div>
              <div class="text-lg text-bold">5000</div>
            </el-col>
          </el-row>
          <!-- 投资策略 -->
          <el-row type="flex" justify="center" class="margin-top">
            <el-col class="trading-card">
              <div class="text-and-button">
                <div>
                  <div>本轮投资策略：</div>
                  <div class="text-lg text-bold margin-top-sm">卖出10%</div>
                </div>
                <el-button type="primary">确认操作</el-button>
              </div>
            </el-col>
          </el-row>
          <!-- 策略解读 -->
          <div class="trading-desc margin-top-lg">
            <div>策略解读：</div>
            <!-- 一号算法 -->
            <div>一号算法：在所有可能的决策里面，找到最低的盈利预期涨跌幅</div>
          </div>
        </el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script setup>
import { ref } from "vue";
let tradingStrategyPerviewList = ref([]);
function onClick() {
  maplist.value = [1, 2, 3, 4, 5, 6];
}
</script>

<style>
body {
  margin: 0;
}
.el-aside {
  background-color: #fafafa;
  padding: 20px;
}
.el-main{
  padding: 0;
}
.step-card {
  background-color: rgb(230, 230, 230);
  border-radius: 5px;
  padding: 20px;
  color: #555;
}
.step-card-choose {
  background-color: #1890ff;
  border-radius: 5px;
  padding: 20px;
  color: #fff;
}
.text-sm {
  font-size: 12px;
}
.text-lg {
  font-size: 24px;
}
.text-bold {
  font-weight: bold;
}
.margin-top-sm {
  margin-top: 5px;
}
.margin-top {
  margin-top: 10px;
}
.margin-top-lg {
  margin-top: 20px;
}
.padding {
  padding: 20px;
}
.el-input {
  background-color: rgb(236, 236, 236);
  border-radius: 50px;
}
.el-input-group__append,
.el-input-group__prepend {
  background-color: #1890ff;
  color: #fff;
  border-radius: 50px;
}
.el-input__inner {
  background-color: rgb(236, 236, 236);
  border: 0px;
  border-radius: 50px;
}
.trading-card {
  color: #0081ff;
  background-color: #cce6ff;
  padding: 20px;
  border-radius: 5px;
}
.text-and-button {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.detail-box{
  text-align: center;
  padding: 20px;
  color: #666;
}
.trading-desc{
  background-color: #fafafa;
  color: #888;
  border-radius: 5px;
  padding: 20px;
}
</style>
