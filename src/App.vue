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
          @click="changePage(cardIndex)"
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
          <!-- 累计收益信息版 -->
          <el-row
            class="trading-desc"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
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
          <!-- 输入本轮涨跌幅 -->
          <el-row
            class="margin-top-lg"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc == false"
          >
            <div>
              <el-input
                placeholder="请输入本轮的涨跌幅,例如：-15（-15%）"
                v-model="inputVol"
              >
                <template v-slot:append>
                  <el-button @click="comfirmButton">提交计算</el-button>
                </template>
              </el-input>
            </div>
          </el-row>
          <!-- 仓位价值 -->
          <el-row
            type="flex"
            justify="space-between"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
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
          <el-row
            type="flex"
            justify="center"
            class="margin-top"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
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
                    tradingPerviewList[perviewChooseIndex].isConfirm == false && tradingPerviewList.length < 12
                  "
                  @click="confirmButton"
                  >确认操作</el-button
                >
              </div>
            </el-col>
          </el-row>
          <!-- 已选择策略 -->
          <div
            class="trading-desc margin-top-lg"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
            <el-row>
              <el-col class="detail-box" :span="6">
                <div class="text-sm">决策操作</div>
                <div class="text-lg text-bold">
                  {{ tradingChooseList[perviewChooseIndex].chooseDetail.title }}
                </div>
              </el-col>
              <el-col class="detail-box" :span="6">
                <div class="text-sm">最大跌幅预期结果</div>
                <div class="text-lg text-bold">
                  {{
                    tradingChooseList[perviewChooseIndex].chooseDetail
                      .maxFallResult
                  }}
                </div>
              </el-col>
              <el-col class="detail-box" :span="6">
                <div class="text-sm">最大跌幅结果减本金</div>
                <div class="text-lg text-bold">
                  {{
                    tradingChooseList[perviewChooseIndex].chooseDetail
                      .reduceFall
                  }}
                </div>
              </el-col>
              <el-col class="detail-box" :span="6">
                <div class="text-sm">回本的最小预期涨幅</div>
                <div class="text-lg text-bold">
                  {{
                    tradingChooseList[perviewChooseIndex].chooseDetail
                      .minExpPaybackRate
                  }}%
                </div>
              </el-col>
            </el-row>
          </div>
          <!-- 所有决策 -->
          <el-table
            :data="tradingStrList[perviewChooseIndex]"
            height="250"
            border
            style="width: 100%"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
            <el-table-column prop="title" label="操作" width="180">
            </el-table-column>
            <el-table-column
              prop="maxFallResult"
              :label="'最大跌幅预期结果 : ' + maxFallText + '%'"
              width="230"
            >
            </el-table-column>
            <el-table-column
              prop="reduceFall"
              label="最大跌幅结果减本金"
              width="180"
            >
            </el-table-column>
            <el-table-column
              prop="minExpPaybackRate"
              label="回本的最小预期涨幅(%)"
            >
            </el-table-column>
          </el-table>
          <!-- 策略解读 -->
          <div
            class="trading-desc margin-top-lg"
            v-if="tradingPerviewList[perviewChooseIndex].isCalc"
          >
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
let inputVol = ref("");
// 左手边的投资预览 {本轮涨跌幅,改变建议,投资结果,是否已提交计算,是否已确认操作}
let tradingPerviewList = ref([]);
// 每轮的投资结果 {涨跌幅,总价值,累计本金,累计收益,累计收益率,可买金额,可卖金额}
let tradingDetailList = ref([]);
// 每轮的投资选择 {chooseIndex: 0, chooseDetail:{xxx} reason: 1}
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
  "为了降低最大预期跌幅可能带来的无法回本的概率，尽可能降低第一次投入的比例",
];
// 投资结果模板 买入1%-100%、卖出1%-100%、不操作
// 标题,交易类型,交易比例,最大预期跌幅结果,最大跌幅结果减本金的差,回本需要的最小预期涨幅
// {title:"买入1%",tradingType:"buy",rate:0.01,maxFallResult:1000,reduceFall:200,minExpPaybackRate:0.2}
let tradingStrList = ref([]);
// 最大预期跌幅
let maxFall = -0.35;
let maxFallText = (maxFall * 100).toFixed(2);

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
    const maxFallResult = (
      buy -
      buy * rate +
      (sell + buy * rate) * (1 + maxFall)
    ).toFixed(2);
    // 目前可卖出金额
    const tempSell = sell + buy * rate;
    // 目前可买入金额
    const tempBuy = buy - buy * rate;
    // 最大跌幅结果减本金的差
    const reduceFall = (maxFallResult - cap).toFixed(2);
    const minExpPaybackRate = calcMinExpVol(cap, buy, sell, tradingType, rate);
    const tempRes = {
      title: title,
      tradingType: tradingType,
      rate: rate,
      buy: tempBuy,
      sell: tempSell,
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
        (buy + sell * rate + (sell - sell * rate) * (1 + maxFall)).toFixed(2);
      // 目前可卖出金额
      const tempSell = sell + buy * rate;
      // 目前可买入金额
      const tempBuy = buy - buy * rate;
      // 最大跌幅结果减本金的差
      const reduceFall = (maxFallResult - cap).toFixed(2);
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
        buy: tempBuy,
        sell: tempSell,
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
      buy: buy,
      sell: sell,
      maxFallResult: hold_maxFallResult.toFixed(2),
      reduceFall: hold_reduceFall.toFixed(2),
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
  return (targetVol * 100).toFixed(2);
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
    chooseDetail: tradingStrList.value[0][0],
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
}
// 确认操作
function confirmButton() {
  // 更新投资状态
  tradingPerviewList.value[perviewChooseIndex.value].isConfirm = true;
  // 创建新的预览页
  const tempTradingPerview = {
    vol: 0,
    suggest: "等待计算",
    result: "等待计算",
    isCalc: false,
    isConfirm: false,
  };
  tradingPerviewList.value.push(tempTradingPerview);
  perviewChooseIndex.value = tradingPerviewList.value.length - 1;
}
// 选择页面
function changePage(index) {
  perviewChooseIndex.value = index;
}
// 提交计算
function comfirmButton() {
  if (Number(inputVol.value) < -100) {
    return;
  }
  // 涨跌幅,总价值,累计本金,累计收益,累计收益率,可买金额,可卖金额
  const tempTradingDetail = {
    vol: 0,
    totalValue: 0,
    cap: 0,
    net: 0,
    netPerCap: 0,
    buy: 0,
    sell: 0,
  };
  tempTradingDetail.vol = Number(inputVol.value).toFixed(2);
  const tempLastIndex = tradingDetailList.value.length - 1;
  const tempLastTradingStrData =
    tradingChooseList.value[tempLastIndex].chooseDetail;
  const tempLastTradingDetailData = tradingDetailList.value[tempLastIndex];
  tempTradingDetail.cap = tempLastTradingDetailData.cap + 1000;
  // 上次操作为买入
  if (tempLastTradingStrData.tradingType == "buy") {
    tempTradingDetail.buy =
      (Number(tempLastTradingDetailData.buy) -
      Number(tempLastTradingDetailData.buy) * tempLastTradingStrData.rate +
      1000).toFixed(2);
    tempTradingDetail.sell = (
      (Number(tempLastTradingDetailData.sell) +
        Number(tempLastTradingDetailData.buy) * tempLastTradingStrData.rate) *
      (1 + Number(tempTradingDetail.vol)/100)
    ).toFixed(2);
  }
  // 上次操作为卖出
  if (tempLastTradingStrData.tradingType == "sell") {
    tempTradingDetail.buy =
      (Number(tempLastTradingDetailData.buy) +
      Number(tempLastTradingDetailData.sell) * tempLastTradingStrData.rate +
      1000).toFixed(2);
    tempTradingDetail.sell = (
      (Number(tempLastTradingDetailData.sell) -
        Number(tempLastTradingDetailData.sell) * tempLastTradingStrData.rate) *
      (1 + Number(tempTradingDetail.vol)/100)
    ).toFixed(2);
  }
  // 上次操作为持仓不动
  if (tempLastTradingStrData.tradingType == "hold") {
    tempTradingDetail.buy = (Number(tempLastTradingDetailData.buy) + 1000).toFixed(2);
    tempTradingDetail.sell = (
      Number(tempLastTradingDetailData.sell) *
      (1 + Number(tempTradingDetail.vol)/100)
    ).toFixed(2);
  }
  tempTradingDetail.totalValue = (Number(tempTradingDetail.buy) + Number(tempTradingDetail.sell)).toFixed(2);
  tempTradingDetail.net = (Number(tempTradingDetail.totalValue) - tempTradingDetail.cap).toFixed(2);
  tempTradingDetail.netPerCap = (
    (Number(tempTradingDetail.net) / tempTradingDetail.cap) *
    100
  ).toFixed(2);
  tradingDetailList.value.push(tempTradingDetail);
  // 更新上一个投资状态
  if (Number(tempTradingDetail.net) > 0) {
    tradingPerviewList.value[tempLastIndex].result = "盈利";
  }
  if (Number(tempTradingDetail.net) == 0) {
    tradingPerviewList.value[tempLastIndex].result = "维持本金";
  }
  if (Number(tempTradingDetail.net) < 0) {
    tradingPerviewList.value[tempLastIndex].result = "亏损";
  }
  // 计算所有可能的投资决策
  calcAllTradingStraregy(
    tempTradingDetail.cap,
    Number(tempTradingDetail.buy),
    Number(tempTradingDetail.sell)
  );
  // 在最大跌幅预期下是否存在保本选择
  let isExistPGChoose = false;
  // 保本的策略列表
  let tempPGTradingList = [];
  const thisTradingStraregyIndex = tradingStrList.value.length - 1;
  const tempTradingStraregyList =
    tradingStrList.value[thisTradingStraregyIndex];
  for (let index = 0; index < tempTradingStraregyList.length; index++) {
    if (tempTradingStraregyList[index].reduceFall >= 0) {
      tempPGTradingList.push(tempTradingStraregyList[index]);
      isExistPGChoose = true;
    }
  }
  // 平均涨跌幅
  let volHistoryTotal = 0;
  for (let index = 0; index < tradingDetailList.value.length; index++) {
    volHistoryTotal = volHistoryTotal + tradingDetailList.value[index].vol;
  }
  const avgVol = volHistoryTotal / tradingDetailList.value.length;
  // 逻辑判断
  if (isExistPGChoose) {
    // 选择最大化预期收益的决策
    let tempMaxExpProFitChoose = tempPGTradingList[0];
    for (let index = 0; index < tempPGTradingList.length; index++) {
      if (tempPGTradingList[index].sell > tempMaxExpProFitChoose) {
        tempMaxExpProFitChoose = tempPGTradingList[index];
      }
    }
    const tempTradingChoose = {
      chooseDetail: tempMaxExpProFitChoose,
      reason: 0,
    };
    tradingChooseList.value.push(tempTradingChoose);
  } else {
    if (avgVol>0) {
      // 把买入的仓位调整到在预期平均涨跌幅的情况下最接近可以回本的比例
      // 目标可卖出金额 * 预期平均涨跌幅 + 净盈亏 = 0
      // 目标可卖出金额 = -1 * 净盈亏 / 预期平均涨跌幅
      const targetSell = -1 * Number(tempTradingDetail.net) / avgVol
      const tempTradingChoose = {
        chooseDetail: {},
        reason: 1,
      };
      let tempDiff = tempTradingStraregyList[0].sell - targetSell
      for (let index = 0; index < tempTradingStraregyList.length; index++) {
        if (tempTradingStraregyList[index].sell - targetSell < tempDiff) {
          tempDiff = tempTradingStraregyList[index].sell - targetSell
          tempTradingChoose.chooseDetail = tempTradingStraregyList[index]
        }
      }
      tradingChooseList.value.push(tempTradingChoose);
    } else {
      // 这种情况代表着历史趋势是往下走的，考虑到目的是最大化盈利概率而不是最大化盈利，所以暂时设定为保持不动
      const tempTradingChoose = {
        chooseDetail: tempTradingStraregyList[tempTradingStraregyList.length-1],
        reason: 2,
      };
      tradingChooseList.value.push(tempTradingChoose);
    }
  }
  // 更新本次的投资状态和建议
  tradingPerviewList.value[tradingPerviewList.value.length - 1].suggest = tradingChooseList.value[tradingPerviewList.value.length - 1].chooseDetail.title
  tradingPerviewList.value[tradingPerviewList.value.length - 1].isCalc = true
  inputVol.value = ""
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
  height: 60px;
  line-height: 60px;
  padding: 0 30px;
  font-size: 30px;
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
