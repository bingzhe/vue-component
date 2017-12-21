<template>
  <div id="city-select">
        <el-select v-model="prov" placeholder="请选择" @change="provchange">
          <el-option
            v-for="option in arr"
            :key="option.name"
            :label="option.name"
            :value="option.name">
          </el-option>
        </el-select>
        <el-select v-model="city" placeholder="请选择" @change="citychange">
          <el-option
            v-for="option in cityArr"
            :key="option.name"
            :label="option.name"
            :value="option.name">
          </el-option>
        </el-select>
        <el-select v-model="district" placeholder="请选择" @change="districtchange">
          <el-option
            v-for="option in districtArr"
            :key="option.name"
            :label="option.name"
            :value="option.name">
          </el-option>
        </el-select>
  </div>
</template>

<script>
import { citydata } from "./citydata.js";

export default {
  data() {
    return {
      arr: citydata,
      prov: "北京",
      city: "北京",
      district: "东城区",
      cityArr: [],
      districtArr: []
    };
  },
  beforeMount: function() {
    this.updateCity();
    this.updateDistrict();
  },
  watch: {
    prov: function() {
      this.city = "";
      this.district = "";
      this.updateCity();
      this.updateDistrict();
    },
    city: function() {
      this.district = "";
      this.districtArr = [];
      this.updateDistrict();
    }
  },
  methods: {
    updateCity: function() {
      for (var i in this.arr) {
        var obj = this.arr[i];
        if (obj.name === this.prov) {
          this.cityArr = obj.sub;
          return;
        }
      }
      this.city = this.cityArr[1].name;
    },
    updateDistrict: function() {
      for (var i in this.cityArr) {
        var obj = this.cityArr[i];
        if (obj.name === this.city) {
          this.districtArr = obj.sub;
          return;
        }
      }
      if (
        this.districtArr &&
        this.districtArr.length > 0 &&
        this.districtArr[1].name
      ) {
        this.district = this.districtArr[1].name;
      } else {
        this.district = "";
      }
    },
    provchange() {
      this.$emit("prov-change", this.prov);
    },
    citychange() {
      this.$emit("city-change", this.city);
    },
    districtchange() {
      this.$emit("district-change", this.district);
    }
  }
};
</script>

<style lang="scss">
@import "src/styles/mixin.scss";
#city-select {
  .el-select {
    .el-input__inner {
      width: 180px;
    }
  }
}
</style>


