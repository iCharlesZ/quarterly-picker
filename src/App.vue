<template>
  <div id="app">
    <div class="content">
      <QuarterlyPicker
        v-model="quarterlyValue1"
        @change="quarterlyChange1"
      >
        <i class="iconfont icon-calendar"></i>
      </QuarterlyPicker>

      <QuarterlyPicker
        :isInterval="false"
        v-model="quarterlyValue2"
        @change="quarterlyChange2"
      >
        <i class="iconfont icon-calendar"></i>
      </QuarterlyPicker>
    </div>
  </div>
</template>

<script>
import QuarterlyPicker from "./components/QuarterlyPicker";

export default {
  name: "App",
  components: {
    QuarterlyPicker
  },
  data() {
    return {
      quarterlyValue1: [],
      quarterlyValue2: ""
    };
  },
  methods: {
    quarterlyChange1(val) {
      console.log(val)
      this.quarterlyValue = val
    },
    quarterlyChange2(val) {
      console.log(val)
      this.quarterlyValue2 = val
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  font-family: PingFang SC, Avenir, Helvetica, Arial, sans-serif;
  font-size: 14px;
}

.content {
  display: flex;
  margin: 100px auto;
  width: 500px;
  justify-content: space-between;
}
</style>
