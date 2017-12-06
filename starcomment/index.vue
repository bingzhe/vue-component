<template>
  <div class="star-comment">
    <div class="star" :class="[starType, {comment:isComment} ]">
        <span 
        v-for="(itemClass, index) in itemClasses"
        :key="index" 
        :class="itemClass" 
        class="star-item"
        @click="selectStar(index)"></span>
    </div>
  </div>
</template>

<script>
/**
 * sl-star
 * @module components/starcomment
 * @desc 星星评价组件
 *
 * @param {Number} size - 星星大小，两个选项（46,28）
 * @param {Number} score - 分数， 四舍五入转化为星星数
 * @param {Boolean} isComment - 是否可以点击评价
 * 
 * 自定义事件 change  参数选中的星星对应的分数 
 *
 * @example
 * <sl-star :size="46" :score="4.5" :isComment="true"></sl-star>
 */
const LENGTH = 5;
const CLS_ON = "on";
const CLS_OFF = "off";

export default {
  props: {
    size: {
      type: Number,
      default: 46
    },
    score: {
      type: Number,
      default: 0
    },
    isComment: {
      type: Boolean,
      default: true
    }
  },
  watch: {
    score(val) {
      this.scores = val;
    }
  },
  data() {
    return {
      scores: 2
    };
  },
  computed: {
    starType() {
      //通过计算属性,返回组装过的类型,用来对应class类型
      return "star-" + this.size;
    },
    itemClasses() {
      let result = [];
      let integer = Math.round(this.scores); //计算所有星星的数量

      for (let i = 0; i < integer; i++) {
        result.push(CLS_ON);
      }

      while (result.length < LENGTH) {
        result.push(CLS_OFF);
      }

      return result;
    }
  },
  methods: {
    selectStar(index) {
      let idx = index + 1;

      if (this.isComment) {
        this.scores = idx;
        this.$emit("change", idx);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "src/style/util.scss";

.star {
  font-size: 0;
  .star-item {
    display: inline-block;
    background-repeat: no-repeat;
    background-size: cover;
  }
}

.star.star-46 {
  .star-item {
    width: px2rem(46);
    height: px2rem(44);
    margin-right: px2rem(22);

    &:last-child {
      margin-right: 0;
    }
    &.on {
      @include bg-image("./images/star46_on");
    }
    &.off {
      @include bg-image("./images/star46_off");
    }
  }
}

.star.star-28 {
  .star-item {
    width: px2rem(28);
    height: px2rem(28);
    margin-right: px2rem(14);

    &:last-child {
      margin-right: 0;
    }
    &.on {
      @include bg-image("./images/star28_on");
    }
    &.off {
      @include bg-image("./images/star28_off");
    }
  }
}

.star.comment {
  .star-item {
    cursor: pointer;
  }
}
</style>

