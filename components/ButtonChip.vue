<template>
  <button
    @click="betChip()"
    :class="disableButton ? 'btn-chip-disable' : 'btn-chip'"
    :disabled="disableButton"
  >
    <slot>Button</slot>
  </button>
</template>

<script>
export default {
  name: "ButtonChip",
  props: ["chip", "amount", "bet_placed"],
  methods: {
    betChip() {
      return this.$emit("calculateAmount", this.chip);
    },
  },
  computed: {
    disableButton() {
      if (this.bet_placed) {
        return true;
      }
      return Number(this.amount) + Number(this.chip) > 3000;
    },
  },
  watch: {
    amount(newValue, oldValue) {
      this.amount = newValue;
    },
  },
};
</script>
<style>
.btn {
  margin: 5px;
}
.btn-chip {
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
.btn-chip-disable {
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

.btn-chip:hover {
  background-color: rgba(34, 135, 215, 255);
}
@media screen and (max-width: 480px) {
  .btn-chip, .btn-chip-disable {
    padding: 0.1rem 0.5rem;
  }
}
</style>
