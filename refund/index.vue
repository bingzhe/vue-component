<template>
  <div id="refund-page">
        <!--头部 start-->
        <v-header :go-back="true" :go-home="true" head-title="退款申请"></v-header>
        <!--头部 end-->
        <div class="gray-bg"></div>
      
        <!-- 订单详情 -->
        <section class="order-detail">
                  <!-- 订单号 -->
            <div class="order-id">
              <span>订单编号: </span>
              <span>{{ order_id }}</span>
            </div>
            <div class="flex-box content">
              <div class="image flex-none">
                  <img :src="imgbase_url + '/img_get.php?img=1&height=80&width=80&imgname=' + food_img_str" />
              </div>
              <div class="detail item-grow  flex-box">
                  <div class="detail-name flex-box">
                      <div class="food-name">{{ food_names}}</div>
                      <div class="state flex-none">{{order_status_str}}</div>
                  </div>

                  <div class="detail-time flex-box">
                      <div class="time-num">
                          <div class="num">
                              <span>数量： </span>
                              <span>{{ food_num_all }}</span>
                          </div>
                          <div class="time">
                              <span>交易时间： </span>
                              <span>{{ order_time }}</span>
                          </div>
                      </div>

                      <div class="price flex-none">
                          ￥{{ food_price_all }}
                      </div>
                  </div>
              </div>
            </div>
        </section>

        <div class="gray-bg"></div>

        <!-- 退款原因 start-->
        <s-radio :options="refundOptions"
        v-model="reReason"
        class="reason-radio"
        @change="getSelect">
        </s-radio>
        
        <!-- <div class="reason-bg"></div> -->

        <div class="reason-text">
          <textarea v-model="reasonText"
          placeholder="请输入其他退款原因"></textarea>
        </div>
        <!-- 退款原因 end -->

        <div class="btn-content">
          <div class="comment-btn" @click="saveReason">
            提交
          </div>
        </div>

        <!-- 退款弹窗 start -->
        <!-- success -->
        <s-dialog :isShow="isShowSuccessTip" @on-close="closeSucDialog">
          <div class="success-content">
            <div class="text">
              提交成功，等待商家处理...
            </div>
            <div class="btn">
              <span @click="closeSucDialog">
                我知道了
              </span>
            </div>
          </div>
        </s-dialog>
        <!-- fail -->
        <s-dialog :isShow="isShowFailTip" @on-close="closeFailDialog">
          <div class="fail-content">
            <div class="text">
              提交失败，请检查网络
            </div>
            <div class="btn">
              <span @click="closeFailDialog">
                我知道了
              </span>
            </div>
          </div>
        </s-dialog>
        <!-- 退款弹窗 end -->

  </div>
</template>

<script>
import VHeader from "@/components/newheader";
import SRadio from "./radio/refundRadio";
import SDialog from "@/components/dialog";

import { Util } from "@/config/util";
import { OrderStatus, ReafundReason } from "@/config/cfg";

export default {
  components: {
    VHeader,
    SRadio,
    SDialog
  },
  data() {
    return {
      imgbase_url: imgbase_url,

      orderinfo: {},
      order_id: "",
      food_price_all: null,
      order_time: null,
      food_num_all: null,
      order_status_str: "",
      food_names: "",
      food_img_str: "",

      refundOptions: [
        {
          label: ReafundReason.toString(ReafundReason.PERSON),
          value: ReafundReason.PERSON
        },
        {
          label: ReafundReason.toString(ReafundReason.SLOW),
          value: ReafundReason.SLOW
        },
        {
          label: ReafundReason.toString(ReafundReason.OTHER),
          value: ReafundReason.OTHER
        }
      ],
      reReason: ReafundReason.PERSON,
      reasonText: "",

      isShowSuccessTip: false,
      isShowFailTip: false
    };
  },
  created() {
    this.getOrderinfo();
  },
  methods: {
    getSelect(select) {
      this.reReason = select;
    },
    getOrderinfo() {
      let id = this.$route.query.orderid;
      let data = {
        get_order_info: 1,
        order_id: id
      };

      Util.DataEncSubmit("/order_get.php", data, resp => {
        if (resp.ret === 0) {
          this.orderinfo = resp.data.info || {};

          this.order_id = this.orderinfo.order_id;
          this.food_price_all = this.orderinfo.food_price_all;
          this.food_num_all = this.orderinfo.food_num_all;

          //处理订单中的餐品名称
          let food_names_arr = [];

          this.orderinfo.food_list.forEach(food => {
            food_names_arr.push(food.food_name);
          });
          this.food_names = food_names_arr.join(" ");

          //处理订单状态
          if (this.orderinfo.order_status === OrderStatus.COMMENT) {
            this.order_status_str = "交易完成";
          } else {
            this.order_status_str = OrderStatus.toString(
              this.orderinfo.order_status
            );
          }

          //取显示的第一张图片
          this.food_img_str = (this.orderinfo.food_img || [])[0];

          this.order_time = Util.TimeTo(this.orderinfo.order_time);
        } else {
          console.warn("获取订单详情失败");
        }
      });
    },
    //保存申请退款原因
    saveReason() {
      let id = this.$route.query.orderid;
      let realReason = "";

      if (this.reReason === ReafundReason.PERSON) {
        realReason = ReafundReason.toString(ReafundReason.PERSON);
      } else if (this.reReason === ReafundReason.SLOW) {
        realReason = ReafundReason.toString(ReafundReason.SLOW);
      } else if (this.reReason === ReafundReason.OTHER) {
        realReason = this.reasonText;
      }

      let data = {
        refund_order: 1, //参数
        order_id: id,
        made_reson: realReason //(string)退款原因
      };
      
      Util.DataEncSubmit("/order_save.php", data, resp => {
        if (resp.ret === 0) {
          this.isShowSuccessTip = true;
        } else {
          this.isShowFailTip = true;
        }
      });
    },
    closeSucDialog() {
      this.isShowSuccessTip = false;
    },
    closeFailDialog() {
      this.isShowFailTip = false;
    }
  }
};
</script>

<style lang="scss" scoped>
@import "src/style/util.scss";

#refund-page {
  @include fc(px2rem(30), #393939);
  margin-top: px2rem(78);
  // position: relative;
}

.gray-bg {
  @include wh(100%, px2rem(20));
  background-color: #f0f0f0;
}
.order-detail .order-id {
  height: px2rem(88);
  line-height: px2rem(88px);
  margin: 0 px2rem(20);
  border-bottom: 1px solid #e7e7e7;
}
.order-detail .content {
  margin: px2rem(30) px2rem(20);
}
.order-detail .content .image {
  @include wh(px2rem(138), px2rem(138));
  border-radius: px2rem(10);
  margin-right: px2rem(20);
  border-radius: px2rem(10);
  img {
    @include wh(px2rem(138), px2rem(138));
    border-radius: px2rem(10);
  }
}
.order-detail .content .detail {
  flex-direction: column;
  justify-content: space-between; //主轴排列方式
  .detail-name {
    @include fc(px2rem(30), #393939);
    justify-content: space-between;
    line-height: px2rem(36);

    .food-name {
      max-width: px2rem(306);
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: normal !important;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
    }

    .state {
      color: $or;
    }
  }
  .detail-time {
    @include fc(px2rem(22), #585858);
    justify-content: space-between;

    .price {
      @include fc(px2rem(30), #393939);
    }
  }
}
.reason-radio {
  margin: px2rem(30) px2rem(14) px2rem(14);
}
.reason-text {
  border: 1px solid #d6d6d6;
  background-color: #fafafa;
  border-radius: px2rem(10);
  @include wh(px2rem(616),px2rem(166));
  // position: absolute;
  bottom: px2rem(30);
  // left: 50%;
  // transform: translateX(-50%);
  margin-left: px2rem(70);
  padding: px2rem(26) px2rem(20);

  textarea {
    background-color: #fafafa;
    @include fc(px2rem(30), #9b9b9b);
    @include wh(100%, 100%);
    overflow: hidden;
    resize: none;
  }
}
.reason-bg {
  @include wh(100%, px2rem(196));
}
.btn-content {
  background-color: #f0f0f0;
  position: absolute;
  top: px2rem(820px);
  bottom: 0;
  left: 0;
  right: 0;
}
.comment-btn {
  @include wh(px2rem(654),px2rem(78));
  line-height: px2rem(78);
  text-align: center;
  border-radius: 5px;
  position: absolute;
  top: px2rem(84);
  left: 50%;
  transform: translateX(-50%);
  color: #fff;
  background: linear-gradient(to right, #fe9f39, #ff6f06);
}

.success-content,
.fail-content {
  @include wh(px2rem(588), px2rem(282));
  border-radius: px2rem(12);
  background-color: #fff;
  position: absolute;
  z-index: 102;
  left: 50%;
  top: px2rem(402);
  transform: translateX(-50%);

  .text {
    @include fc(px2rem(32px),#393939);
    text-align: center;
    height: px2rem(184);
    line-height: px2rem(184);
    border-bottom: 1px solid #e7e7e7;
  }
  .btn {
    @include fc(px2rem(36px),#ff6f06);
    text-align: center;
    height: px2rem(98);
    line-height: px2rem(98);
  }
}
textarea::-webkit-input-placeholder {
  color: #9b9b9b;
}
textarea:-moz-placeholder {
  color: #9b9b9b;
}
textarea::-moz-placeholder {
  color: #9b9b9b;
}
textarea::-ms-input-placeholder {
  color: #9b9b9b;
}
</style>

