<template>
  <div class="sl-errtip" v-if="visible">
      <div class="sl-dialog-content">
          {{ message }}
      </div>
  </div>
</template>

<script>
// this.$slnotify({
//   message: "注册失败"
// });
export default {
  data() {
    return {
      visible: false,
      message: "",
      duration: 2000,
      timer: null
    };
  },
  mounted() {
    if (this.duration > 0) {
      this.timer = setTimeout(() => {
        this.visible = false;
      }, this.duration);
    }
  }
};
</script>

<style lang="scss" scoped>
@import "src/style/util.scss";
.sl-errtip {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 99999;
}
.sl-dialog-content {
  @include fc(px2rem(30), #fff);
  padding: px2rem(40);
  max-width: px2rem(420);
  min-width: px2rem(290);
  opacity: 0.65;
  background: #000000;
  color: #fff;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 999999;
  border-radius: px2rem(10);
  text-align: center;
  line-height: px2rem(36);
  font-size: 14px;
}
</style>
