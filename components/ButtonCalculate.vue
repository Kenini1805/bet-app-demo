<template>
  <button
    @click="calculate()"
    :class="disableButton ? 'btn btn-disable' : 'btn btn-calculate'"
    :disabled="disableButton"
  >
    <slot>Button</slot>
  </button>
</template>

<script>
export default {
  name: "ButtonCalculate",
  props: ["value", "operator", "chip", "bet_placed"],
  methods: {
    calculate() {
      return this.$emit("calculateOperator", {
        value: this.value,
        operator: this.operator,
        chip: this.chip,
      });
    },
  },
  computed: {
    disableButton() {
      if (this.bet_placed) {
        return true;
      }
      if (
        parseFloat(this.value).toFixed(2) <= 1.1 &&
        this.operator === "subtraction"
      ) {
        return true;
      }
      if (Number(this.value) === 99 && this.operator === "summation") {
        return true;
      }
      return Number(this.value) * Number(this.chip) > 3000;
    },
  },
  watch: {
    amount(newValue, oldValue) {
      this.value = newValue;
    },
  },
};
</script>
<style>
.btn {
  margin: 5px;
}
.btn-calculate {
  background-color: #21354e;
  color: #47bcc5;
  display: inline-block;
  font-weight: 400;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  border: 1px solid transparent;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  line-height: 1.5;
  border-radius: 0.25rem;
  cursor: pointer;
}
.btn-disable {
  background-color: #1a2134;
  color: #283b59;
  display: inline-block;
  font-weight: 400;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  border: 1px solid #283b59;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  line-height: 1.5;
  border-radius: 0.25rem;
}
.btn-calculate:hover {
  background-color: rgba(34, 135, 215, 255);
}
</style>
