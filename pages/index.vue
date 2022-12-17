<template>
  <div class="box">
    <div class="range">
      <div class="content">
        <div class="min-max">
          <v-button-calculate
            :value="amount"
            :operator="'min'"
            :bet_placed="bet_placed"
            @calculateOperator="calculateOperator($event)"
            >MIN</v-button-calculate
          >
          <v-button-calculate
            :value="amount"
            :operator="'max'"
            :bet_placed="bet_placed"
            @calculateOperator="calculateOperator($event)"
            >MAX</v-button-calculate
          >
        </div>
        <div class="money-amount">
          <span class="dollar">¥</span>
          <input
            @keypress="isNumber($event)"
            class="amount"
            @mouseout="checkAmount"
            :disabled="bet_placed"
            v-model="amount"
          />
        </div>
        <div class="half-double">
          <v-button-calculate
            :value="amount"
            :bet_placed="bet_placed"
            :chip="HAFT_AMOUNT"
            :operator="'multiplication'"
            @calculateOperator="calculateOperator($event)"
            >1/2</v-button-calculate
          >
          <v-button-calculate
            :value="amount"
            :bet_placed="bet_placed"
            :chip="DOUBLE_AMOUNT"
            :operator="'multiplication'"
            @calculateOperator="calculateOperator($event)"
            >2X</v-button-calculate
          >
        </div>
      </div>
      <hr />
      <div class="chip-coin">
        <div class="chip" v-for="(chip, key) in chips" :key="key">
          <v-button-chip
            :chip="key"
            :bet_placed="bet_placed"
            :amount="amount"
            @calculateAmount="calculateAmount($event)"
            >{{ chip }}</v-button-chip
          >
        </div>
      </div>
    </div>

    <div class="defuse">
      <div class="defuse-body">
        <p class="defuse-at-text">DEFUSE AT</p>
        <div class="btn-defuse">
          <v-button-calculate
            :value="defuse"
            :bet_placed="bet_placed"
            :operator="'subtraction'"
            @calculateOperator="calculateOperator($event)"
            :disabled="isMinimum"
            >-</v-button-calculate
          >
          <input
            class="defuse-at-number"
            @keypress="isNumberAndDot($event)"
            :disabled="bet_placed"
            @mouseout="checkDefuse"
            v-model.number="solicitorsFeesDisplay"
          />
          <v-button-calculate
            :value="defuse"
            :bet_placed="bet_placed"
            :operator="'summation'"
            @calculateOperator="calculateOperator($event)"
            :disabled="isMaximum"
            >+</v-button-calculate
          >
        </div>
      </div>
      <hr class="hr-defuse" />

      <div class="chip-coin chip-coin-defuse">
        <div class="chip" v-for="(defuse, key) in defuses" :key="key">
          <v-button-chip
            :bet_placed="bet_placed"
            :chip="key"
            @calculateAmount="calculateDefuse($event)"
            >{{ defuse }}</v-button-chip
          >
        </div>
      </div>
    </div>

    <div v-if="!bet_placed" @click="bet()" class="bet">
      <span>BET</span>
    </div>
    <div v-else class="bet bet-placed">
      <span>BET PLACE</span>
    </div>
  </div>
</template>

<script>
import ButtonCalculateVue from "../components/ButtonCalculate.vue";
import ButtonChipVue from "../components/ButtonChip.vue";
export default {
  head: {
    bodyAttrs: {
      class: "home-page",
    },
  },
  name: "IndexPage",
  components: {
    "v-button-calculate": ButtonCalculateVue,
    "v-button-chip": ButtonChipVue,
  },
  data() {
    return {
      chips: {
        5: "+5",
        10: "+10",
        50: "+50",
        100: "+100",
        500: "+500",
        1000: "+1K",
      },
      defuses: {
        1.5: "1.5x",
        2: "2.0x",
        5: "5.0x",
      },
      amount: 5,
      defuse: 2.0,
      MIN_DEFUSE: 1.1,
      MAX_DEFUSE: 99,
      DOUBLE_AMOUNT: 2,
      HAFT_AMOUNT: 0.5,
      bet_placed: false,
    };
  },
  methods: {
    formatDefuses(num) {
      return parseFloat(num).toFixed(2);
    },
    calculateAmount(chip) {
      return (this.amount = Number(this.amount) + Number(chip));
    },
    calculateDefuse(defuse) {
      return (this.defuse = Number(defuse));
    },
    bet() {
      this.bet_placed = !this.bet_placed;
    },
    calculateOperator(data) {
      if (data.operator === "subtraction") {
        this.defuse = Number(this.defuse) - 0.1;
      }
      if (data.operator === "summation") {
        if (Number(this.defuse) < 2) {
          this.defuse = Number(this.defuse) + 0.1;
        } else {
          this.defuse = Number(this.defuse) + 1;
        }
      }
      if (data.operator == "min") {
        this.amount = 10;
      }
      if (data.operator == "max") {
        this.amount = 3000;
      }
      if (data.operator == "multiplication") {
        this.amount = Number(this.amount) * Number(data.chip);
      }
    },
    checkAmount() {
      if (this.amount < 5) {
        return (this.amount = 5);
      }
      if (this.amount > 3000) {
        return (this.amount = 3000);
      }
    },
    checkDefuse() {
      if (this.defuse < 1.01 || isNaN(this.defuse)) {
        return (this.defuse = 1.01);
      }
      if (this.defuse > 99) {
        return (this.defuse = 99);
      }
    },
    isNumber: function (evt) {
      evt = evt ? evt : window.event;
      var charCode = evt.which ? evt.which : evt.keyCode;
      if (charCode > 31 && (charCode < 48 || charCode > 57)) {
        evt.preventDefault();
      } else {
        return true;
      }
    },
    isNumberAndDot: function (evt) {
      evt = evt ? evt : window.event;
      var charCode = evt.which ? evt.which : evt.keyCode;
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 46
      ) {
        evt.preventDefault();
      } else {
        return true;
      }
    },
  },
  computed: {
    isMinimum() {
      return this.defuse < this.MIN_DEFUSE;
    },
    isMaximum() {
      return this.defuse >= this.MAX_DEFUSE;
    },
    solicitorsFeesDisplay: {
      get: function () {
        if (this.defuse) {
          return parseFloat(this.defuse).toFixed(2);
        }
      },
      set: function (newValue) {
        this.defuse = newValue;
      },
    },
  },
};
</script>
<style>
hr {
  width: 100%;
  border: 0.5px solid #2c364f;
}
.home-page {
  background-color: #09131e;
}

.box,
.content {
  display: flex;
  flex-direction: row;
}
.range,
.defuse,
.bet {
  flex: 100%;
  margin: 10px;
  background-color: #1b2234;
  border: 1px solid #2c364f;
  border-radius: 25px;
}
.bet-placed {
  background-color: #1b2234 !important;
}
.bet {
  text-align: center;
  font-size: 25px;
  color: #fbf9f6;
  background-color: rgba(34, 135, 215, 255);
  cursor: pointer;
}
.money-amount {
  display: flex;
  flex: 1;
  text-align: center;
  min-width: 50%;
}

.defuse {
  display: flex;
  text-align: center;
  flex-direction: column;
}
.defuse-at-text {
  color: #435066;
}
.min-max,
.half-double,
.dollar {
  display: flex;
  flex-direction: column-reverse;
  margin: 5px;
}
.amount,
.dollar {
  margin: auto;
  display: block;
  font-size: 25px;
  color: #fbf9f6;
  background-color: #1b2234;
}
.defuse-at-number {
  font-size: 25px;
  color: #fbf9f6;
}
.chip-coin {
  display: flex;
  align-items: center;
  justify-content: center;
}
.chip {
  margin: 0px 10px 10px 10px;
}
.bet span {
  min-height: 100%;
  display: inline-flex;
  align-items: center;
}
input {
  all: unset;
}
.btn-defuse {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
.defuse-body {
  margin-bottom: 3px;
}
@media screen and (max-width: 480px) {
  .box {
    flex-direction: column;
    margin-right: 0;
    margin-bottom: 20px;
  }
  .chip {
    margin: 0px 2px 10px 2px;
  }
  .defuse-at {
    flex-direction: row;
  }
  .defuse-body {
    display: flex;
    flex-direction: row;
  }
  .defuse-at-text {
    white-space: nowrap;
    margin-left: 20px;
  }
  .defuse-at-number {
    width: 30%;
  }
  .hr-defuse,
  .chip-coin-defuse {
    display: none;
  }
}
</style>
