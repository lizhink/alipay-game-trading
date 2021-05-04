<template>
  <el-container>
    <el-aside width="250px" style="height: 100vh">
      <div
        v-for="(cardItem, cardIndex) in tradingPerviewList"
        :key="'card' + cardIndex"
      >
        <div
          :class="
            perviewChooseIndex == cardIndex ? 'step-card-choose' : 'step-card'
          "
        >
          <div v-if="cardIndex == 0">初始投资</div>
          <div v-else>第{{ cardIndex }}次投资</div>
          <el-row class="margin-top">
            <el-col :span="12">
              <div class="text-sm">投资建议</div>
              <div class="text-bold">{{ cardItem.suggest }}</div>
            </el-col>
            <el-col :span="12">
              <div class="text-sm">投资结果</div>
              <div class="text-bold">{{ cardItem.result }}</div>
            </el-col>
          </el-row>
        </div>
      </div>
    </el-aside>
    <el-main>
      <el-row type="flex" justify="center" align="middle" style="height: 100vh">
        <el-col :xs="22" :sm="20" :md="18" :lg="16" :xl="14">
          <el-row class="trading-desc">
            <el-col class="detail-box" :span="8">
              <div class="text-xl text-bold">
                {{ tradingDetailList[perviewChooseIndex].net }}
              </div>
              <div class="text-sm">累计收益</div>
            </el-col>
            <el-col class="detail-box" :span="8">
              <div class="text-xl text-bold">
                {{ tradingDetailList[perviewChooseIndex].netPerCap }}%
              </div>
              <div class="text-sm">累计收益率</div>
            </el-col>
            <el-col class="detail-box" :span="8">
              <div class="text-xl text-bold">{{ winTimes }}</div>
              <div class="text-sm">盈利次数</div>
            </el-col>
          </el-row>
          <el-row
            class="margin-top-lg"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc == false"
          >
            <div>
              <el-input
                placeholder="请输入本轮的涨跌幅,例如：-15（-15%）"
                v-model="inputVol"
              >
                <template v-slot:append>提交计算</template>
              </el-input>
            </div>
          </el-row>
          <!-- 仓位价值 -->
          <el-row type="flex" justify="space-between">
            <el-col class="detail-box" :span="4">
              <div class="text-sm">涨跌幅</div>
              <div class="text-lg text-bold">
                {{ tradingDetailList[perviewChooseIndex].vol }}%
              </div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">总价值</div>
              <div class="text-lg text-bold">
                {{ tradingDetailList[perviewChooseIndex].totalValue }}
              </div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">累计本金</div>
              <div class="text-lg text-bold">
                {{ tradingDetailList[perviewChooseIndex].cap }}
              </div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">可买金额</div>
              <div class="text-lg text-bold">
                {{ tradingDetailList[perviewChooseIndex].buy }}
              </div>
            </el-col>
            <el-col class="detail-box" :span="4">
              <div class="text-sm">可卖金额</div>
              <div class="text-lg text-bold">
                {{ tradingDetailList[perviewChooseIndex].sell }}
              </div>
            </el-col>
          </el-row>
          <!-- 投资策略 -->
          <el-row type="flex" justify="center" class="margin-top">
            <el-col class="trading-card">
              <div class="text-and-button">
                <div>
                  <div>本轮投资策略：</div>
                  <div class="text-lg text-bold margin-top-sm">
                    {{ tradingPerviewList[perviewChooseIndex].suggest }}
                  </div>
                </div>
                <el-button
                  type="primary"
                  v-if="
                    tradingPerviewList[perviewChooseIndex].isConfirm == false
                  "
                  >确认操作</el-button
                >
              </div>
            </el-col>
          </el-row>
          <!-- 策略解读 -->
          <div class="trading-desc margin-top-lg">
            <div>策略解读：</div>
            <div class="margin-top">
              {{ decisionReason[tradingChooseList[perviewChooseIndex].reason] }}
            </div>
          </div>
        </el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script setup>
import { ref } from "vue";
// 盈利次数
let winTimes = ref(0);
// 选中预览
let perviewChooseIndex = ref(0);
// 输入涨跌幅
let inputVol = "";
// 左手边的投资预览 {本轮涨跌幅,改变建议,投资结果,是否已提交计算,是否已确认操作}
let tradingPerviewList = ref([]);
// 每轮的投资结果 {涨跌幅,总价值,累计本金,累计收益,可买金额,可卖金额}
let tradingDetailList = ref([]);
// 每轮的投资选择 {chooseIndex: 0, reason: 1}
let tradingChooseList = ref([]);
// 决策解释
const decisionReason = [
  // 算法a结果数量 > 0,执行算法b
  "在所有可能的决策中包含了大概率保本的决策，选择其中最大化预期收益",
  // 算法a结果数量 == 0, 且平均涨跌幅 > 0
  "在所有可能的决策中不包含大概率保本的决策，但是平均涨跌幅大于0，还是有概率能回本，所以调整仓位在预期平均涨幅的情况下最接近可以回本的比例",
  // 算法a结果数量 == 0, 且平均涨跌幅 <= 0
  "在所有可能的决策中不包含大概率保本的决策，且平均涨跌幅小于0，无法回本的概率较大，加大投入或者减少投入都有可能造成不可挽回的损失，所以暂时不调整仓位",
  // 初始投资
  "为了把不可控风险和降低最大预期跌幅可能带来的无法回本的可能，尽可能降低第一次投入的比例",
];
// 投资结果模板 买入1%-100%、卖出1%-100%、不操作
// 标题,交易类型,交易比例,最大预期跌幅结果,最大跌幅结果减本金的差,回本需要的最小预期涨幅
// {title:"买入1%",tradingType:"buy",rate:0.01,maxFallResult:1000,reduceFall:200,minExpPaybackRate:0.2}
let tradingStrList = ref([]);
// 最大预期跌幅
let maxFall = -0.35;

// 计算所有可能的交易策略
// 参数(本金,可买金额,可买金额)
function calcAllTradingStraregy(cap, buy, sell) {
  // 暂放结果
  let tempResList = [];
  // 买入1% - 100%
  for (let index = 1; index < 101; index++) {
    const rate = index / 100;
    const title = "买入" + index + "%";
    const tradingType = "buy";
    // 最大预期跌幅结果
    const maxFallResult =
      buy - buy * rate + (sell + buy * rate) * (1 + maxFall);
    // 最大跌幅结果减本金的差
    const reduceFall = maxFallResult - cap;
    const minExpPaybackRate = calcMinExpVol(cap, buy, sell, tradingType, rate);
    const tempRes = {
      title: title,
      tradingType: tradingType,
      rate: rate,
      maxFallResult: maxFallResult,
      reduceFall: reduceFall,
      minExpPaybackRate: minExpPaybackRate,
    };
    tempResList.push(tempRes);
  }
  if (tradingStrList.value.length > 0) {
    // 卖出1% - 100%
    for (let index = 1; index < 101; index++) {
      const rate = index / 100;
      const title = "卖出" + index + "%";
      const tradingType = "sell";
      // 最大预期跌幅结果
      const maxFallResult =
        buy + sell * rate + (sell - sell * rate) * (1 + maxFall);
      // 最大跌幅结果减本金的差
      const reduceFall = maxFallResult - cap;
      const minExpPaybackRate = calcMinExpVol(
        cap,
        buy,
        sell,
        tradingType,
        rate
      );
      const tempRes = {
        title: title,
        tradingType: tradingType,
        rate: rate,
        maxFallResult: maxFallResult,
        reduceFall: reduceFall,
        minExpPaybackRate: minExpPaybackRate,
      };
      tempResList.push(tempRes);
    }
    // 不做操作
    const hold_rate = 0;
    const hold_title = "持仓不动";
    const hold_tradingType = "hold";
    // 最大预期跌幅结果
    const hold_maxFallResult = buy + sell * (1 + maxFall);
    // 最大跌幅结果减本金的差
    const hold_reduceFall = hold_maxFallResult - cap;
    const hold_minExpPaybackRate = calcMinExpVol(
      cap,
      buy,
      sell,
      hold_tradingType,
      hold_rate
    );
    const hold_tempRes = {
      title: hold_title,
      tradingType: hold_tradingType,
      rate: hold_rate,
      maxFallResult: hold_maxFallResult,
      reduceFall: hold_reduceFall,
      minExpPaybackRate: hold_minExpPaybackRate,
    };
    tempResList.push(hold_tempRes);
  }
  tradingStrList.value.push(tempResList);
}
// 计算回本最小预期涨跌幅(涨跌幅后总资产等于本金)
// 参数(本金,可买金额,可买金额,操作方向,操作比例)
function calcMinExpVol(cap, buy, sell, tradingType, rate) {
  let targetVol = 0;
  // 投资后的总资产 = 本金
  if (tradingType == "buy") {
    // (buy - buy * rate) + (sell + buy * rate) * (1 + targetVol) = cap
    targetVol = (cap - buy + buy * rate) / (sell + buy * rate) - 1;
  }
  if (tradingType == "sell") {
    // (buy + sell * rate) + (sell - sell * rate) * (1 + targetVol) = cap
    targetVol = (cap - buy - sell * rate) / (sell - sell * rate) - 1;
  }
  if (tradingType == "hold") {
    // buy + sell * (1 + targetVol) = cap
    targetVol = (cap - buy) / sell - 1;
  }
  return targetVol;
}
// 初始投资
function firstTrading() {
  // 录入初始资金
  // 涨跌幅,总价值,累计本金,累计收益,累计收益率,可买金额,可卖金额
  const tempTradingDetail = {
    vol: 0,
    totalValue: 10000,
    cap: 10000,
    net: 0,
    netPerCap: 0,
    buy: 10000,
    sell: 0,
  };
  tempTradingDetail.net = tempTradingDetail.totalValue - tempTradingDetail.cap;
  tempTradingDetail.netPerCap = (
    (tempTradingDetail.net / tempTradingDetail.cap) *
    100
  ).toFixed(2);
  tradingDetailList.value.push(tempTradingDetail);
  // 调用计算所有可能的交易策略
  calcAllTradingStraregy(
    tempTradingDetail.cap,
    tempTradingDetail.buy,
    tempTradingDetail.sell
  );
  // 做出投资选择
  const tempTradingChoose = {
    chooseIndex: 0,
    reason: 3,
  };
  tradingChooseList.value.push(tempTradingChoose);
  // 更新投资状态
  const tempTradingPerview = {
    vol: 0,
    suggest: "买入1%",
    result: "等待计算",
    isCalc: true,
    isConfirm: false,
  };
  tradingPerviewList.value.push(tempTradingPerview);
  console.log(tradingStrList.value);
}

// 开始构建
firstTrading();
</script>

<style>
body {
  margin: 0;
}
.el-aside {
  background-color: #fafafa;
  padding: 20px;
}
.el-main {
  padding: 0;
}
.step-card {
  background-color: rgb(230, 230, 230);
  border-radius: 5px;
  padding: 20px;
  color: #555;
  margin-bottom: 10px;
}
.step-card-choose {
  background-color: #1890ff;
  border-radius: 5px;
  padding: 20px;
  color: #fff;
  margin-bottom: 10px;
}
.text-sm {
  font-size: 12px;
}
.text-lg {
  font-size: 24px;
}
.text-xl {
  font-size: 36px;
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
.detail-box {
  text-align: center;
  padding: 20px;
  color: #666;
}
.trading-desc {
  background-color: #fafafa;
  color: #888;
  border-radius: 5px;
  padding: 20px;
}
</style>
