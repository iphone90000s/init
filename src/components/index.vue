<template>
  <div>
    <div class="title">定期定額投資試算</div>
    <div class="content">
      <div class="box">
        <label>您預計每月投資多少呢</label>
        <input class="right-txt" type="text" v-model="money" />
        <div v-if="moneyError" class="wrong">{{moneyError}}</div>
      </div>
      <div class="box">
        <label>您預計投資的年份</label>
        <input class="right-txt" type="text" v-model="years" />
        <div v-if="yearsError" class="wrong">{{yearsError}}</div>
      </div>
      <div class="box">
        <label>預期年化報酬率(%)</label>
        <input class="right-txt" type="text" v-model="roi" />
        <div v-if="roiError" class="wrong">{{roiError}}</div>
      </div>
      <button @click="count()" type="button" class="btns">開始計算</button>
      <div class="prompt">此站僅提供試算功能，並不具任何保證效益，實際金額以您的投資產品為主</div>
    </div>
    <div class="blackFrame" v-if="show" @click="closeFrame($event)" ref="black">
      <div class="tables">
        <button @click="switchBtn()" type="button" class="btns btn-style">{{switchTxt}}</button>
        <div class="tables-inner" v-if="resultShow">
          <div class="inner-title">單利結果，每月投資{{money}}，持續{{years}}年</div>
          <table>
            <tr>
              <th>年分</th>
              <th>累積投資金額</th>
              <th>該年利息</th>
              <th>累積利息</th>
            </tr>
            <tr v-for="(list,index) in roiData" :key="index">
              <td>第{{list.year}}年</td>
              <td>{{list.money}}</td>
              <td>{{list.single_bouns}}</td>
              <td>{{list.totalbouns}}</td>
            </tr>
          </table>
        </div>
        <div class="tables-inner" v-if="!resultShow">
          <div class="inner-title">複利結果，每月投資{{money}}，並持續投入{{years}}年</div>
          <table>
            <tr>
              <th>年分</th>
              <th>累積投資金額</th>
              <th>該年利息</th>
              <th>累積利息</th>
            </tr>
            <tr v-for="(list,index) in doubleData" :key="index">
              <td>第{{list.year}}年</td>
              <td>{{list.money}}</td>
              <td>{{list.doubleb_bouns}}</td>
              <td>{{list.totalbouns}}</td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      switchTxt: "切換至複利結果",
      show: false,
      resultShow: true,
      money: "",
      roiData: {},
      doubleData: {},
      years: "",
      roi: "",
      moneyError: false,
      yearsError: false,
      roiError: false
    };
  },
  components: {},
  methods: {
    switchBtn: function() {
      if (this.resultShow) {
        this.resultShow = false;
        this.switchTxt = "切換至單利結果";
      } else {
        this.resultShow = true;
        this.switchTxt = "切換至複利結果";
      }
    },
    closeFrame: function(event) {
      if (event.target.className == "blackFrame") {
        this.show = false;
      }
    },
    count: function() {
      if (this.money == "") {
        this.moneyError = "請輸入您預計每月投資金額";
      } else {
        this.moneyError = false;
      }
      if (this.years == "") {
        this.yearsError = "請輸入您預計投資的年份";
      } else {
        this.yearsError = false;
      }
      if (this.roi == "") {
        this.roiError = "請輸入您預期的年化報酬率";
      } else {
        this.roiError = false;
      }
      if (this.moneyError || this.yearsError || this.roiError) {
        return;
      }
      const vm = this;
      for (let i = 1; i <= vm.years; i++) {
        let new_num = `year${i}`;
        let money = i * vm.money * 12;
        let single_bouns = (i * vm.money * 12 * vm.roi) / 100;
        let totalbouns =
          (i * vm.money * 12 * vm.roi) / 100 +
          ((i - 1) * vm.money * 12 * vm.roi) / 100;
        vm.$set(vm.roiData, new_num, {
          year: i,
          money: money,
          single_bouns: single_bouns,
          totalbouns: totalbouns
        });
      }
      let last = 0;
      for (let i = 1; i <= vm.years; i++) {
        let new_num = `year${i}`;
        let money = i * vm.money * 12 + last; //年投入
        let doubleb_bouns = (money * vm.roi) / 100;
        last = last + doubleb_bouns;
        vm.$set(vm.doubleData, new_num, {
          year: i,
          money: money.toFixed(0),
          doubleb_bouns: doubleb_bouns.toFixed(0),
          totalbouns: last.toFixed(0)
        });
      }
      this.show = true;
    }
  },
  created() {},
  watch: {
    money: function() {
      this.money = this.money.replace(/[^\d]/g, "");
    },
    year: function() {
      this.years = this.years.replace(/[^\d]/g, "");
    },
    roi: function() {
      this.roi = this.roi.replace(/[^\d]/g, "");
    }
  }
};
</script>
