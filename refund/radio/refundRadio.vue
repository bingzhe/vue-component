<template>
  <div class="sl-radiolist" @change="$emit('change', currentValue)">
      <div class="sl-radio-item" v-for="(option, index) in options" :key="index">
          <label class="sl-radiolist-label">
              <span class="sl-radio"
              :class="{active:currentValue === option.value}">
                  <input type="radio"
                    class="sl-radio-input"
                    v-model="currentValue"
                    :disabled="option.disabled"
                    :value="option.value || option">
                    <span class="sl-radio-core"></span>
              </span>
              <span class="sl-radio-label" v-text="option.label || option"></span>
          </label>
      </div>
  </div>
</template>

<script>
/**
 * sl-radio
 * @module components/radio
 * @desc 单选框列表
 *
 * @param {string[], object[]} options - 选项数组，可以传入 [{label: 'label', value: 'value', disabled: true}] 或者 ['ab', 'cd', 'ef']
 * @param {string} value - 选中值
 *
 * @example
 * <sl-radio v-model="value" :options="['a', 'b', 'c']"></sl-radio>
 */
export default {
  name: "sl-radio",

  props: {
    options: {
      type: Array,
      required: true
    },
    value: Number
  },
  data() {
    return {
      currentValue: this.value
    };
  },
  watch: {
    value(val) {
      this.currentValue = val;
    },
    currentValue(val) {
      this.$emit("input", val);
    }
  }
};
</script>

<style lang="scss" scoped>
@import "src/style/util.scss";

.sl-radiolist-label {
  display: block;
  height: px2rem(44);
  line-height: px2rem(44);
}
.sl-radio-item {
  height: px2rem(44);
  line-height: px2rem(44);
  margin-bottom: px2rem(26);
}
.sl-radio-item:last-child {
  margin-bottom: 0;
}
.sl-radio .sl-radio-core {
  display: inline-block;
  @include wh(px2rem(44),px2rem(44));
  @include bg-image("./images/radio-disabled");
  background-size: cover;
  vertical-align: bottom;
  box-sizing: border-box;
}

.sl-radio.active .sl-radio-core {
  @include bg-image("./images/radio-icon");
}
.sl-radio input {
  display: none;
}
.sl-radio-label {
  padding-left: px2rem(14);
}
</style>
