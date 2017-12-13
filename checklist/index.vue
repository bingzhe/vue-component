<template>
  <div class="sl-checklist" @change="$emit('change', currentValue)">
      <div class="sl-check-item" v-for="(option, index) in options" :key="index">
          <label class="sl-checklist-label">
              <span class="sl-check"
              :class="{active:currentValue.indexOf(option.value) !== -1}">
                  <input type="checkbox"
                    class="sl-check-input"
                    v-model="currentValue"
                    :disabled="option.disabled"
                    :value="option.value || option">
                    <span class="sl-check-core" v-text="option.label || option"></span>
              </span>
              <!-- <span class="sl-check-label" v-text="option.label || option"></span> -->
          </label>
      </div>
  </div>
</template>

<script>
/**
 * sl-radio
 * @module components/check
 * @desc 多选列表
 *
 * @param {string[], object[]} options - 选项数组，可以传入 [{label: 'label', value: 'value', disabled: true}] 或者 ['ab', 'cd', 'ef']
 * @param {string} value - 选中值
 *
 * @example
 * <sl-radio v-model="value" :options="['a', 'b', 'c']"></sl-radio>
 */
export default {
  name: "sl-check",

  props: {
    options: {
      type: Array,
      required: true
    },
    value: {
      type: Array,
      default: () => {
        return [];
      }
    }
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

.sl-check input {
  display: none;
  &:checked {
    + .sl-check-core {
      border: 1px solid #ff6f0f;
      color: #ff6f0f;
      background: #fff7f0;
    }
  }
}
.sl-checklist {
  @include fc(px2rem(22), #393939);
}
.sl-check-item {
  display: inline-block;
  margin-right: px2rem(20);
}
.sl-check-item:last-child {
  margin-right: 0;
}
.sl-check-core {
  display: inline-block;
  @include wh(px2rem(122), px2rem(40));
  border: 1px solid #acacac;
  border-radius: px2rem(6);
  text-align: center;
  line-height: px2rem(40);
}
</style>
