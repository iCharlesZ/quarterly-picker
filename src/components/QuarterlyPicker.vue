<template>
  <div
    class="quarterly-picker"
    :class="{interval: isInterval}"
  >
    <div
      class="mask"
      v-show="showChooseBox"
      @click="handlerCancel"
    ></div>
    <input
      v-model="showQuarterly"
      readOnly
      :placeholder="isInterval ? placeholderArr : placeholderStr"
      @focus="openChooseBox()"
    />
    <div class="right-icon"><slot></slot></div>
    <div class="quarterly-choose-box" v-show="showChooseBox">
      <div class="box-left">
        <div class="box-year">
          <button @click="leftYear--">
            <i class="iconfont icon-arrow-right"></i>
          </button>
          <span>{{ leftYear }}</span>
          <button
            :disabled="(leftYear >= rightYear - 1 && isInterval)"
            @click="(leftYear < rightYear - 1 || !isInterval) && leftYear++"
          >
            <i class="iconfont icon-arrow-right"></i>
          </button>
        </div>
        <div class="choose-box">
          <div class="choose-list">
            <div
              class="choose-item"
              v-for="(item, index) in typeDictionary"
              :key="index"
              :class="{
                'choose-item-active':
                  periodDataCopy instanceof Array
                    ? periodDataCopy.some(
                        (element) => element === `${leftYear + item.value}`
                      )
                    : periodDataCopy === leftYear + item.value,
              }"
              @click="
                handlerItemClick(
                  `${leftYear + item.value}`,
                  `${leftYear}年${item.name}`
                )
              "
            >
              {{ item.name }}
            </div>
          </div>
        </div>
      </div>
      <div class="box-right" v-if="isInterval">
        <div class="box-year">
          <button
            :disabled="leftYear <= rightYear - 1"
            @click="rightYear > leftYear + 1 && rightYear--"
          >
            <i class="iconfont icon-arrow-right"></i>
          </button>
          <span>{{ rightYear }}</span>
          <button @click="rightYear++">
            <i class="iconfont icon-arrow-right"></i>
          </button>
        </div>
        <div class="choose-box">
          <div class="choose-list">
            <div
              class="choose-item"
              v-for="(item, index) in typeDictionary"
              :key="index"
              :class="{
                'choose-item-active':
                  periodDataCopy instanceof Array
                    ? periodDataCopy.some(
                        (element) => element === `${rightYear + item.value}`
                      )
                    : periodDataCopy === rightYear + item.value,
              }"
              @click="
                handlerItemClick(
                  `${rightYear + item.value}`,
                  `${rightYear}年${item.name}`
                )
              "
            >
              {{ item.name }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'QuarterlyPicker',
  model: {
    prop: "periodData",
    event: "change"
  },
  props: {
    // 是否是区间
    isInterval: {
      type: Boolean,
      default: true,
    },
    periodData: {
      type: [Array, String],
      default: () => []
    }
  },
  data() {
    return {
      typeDictionary: [
        {
          name: "第一季度",
          value: "Q1"
        },
        {
          name: "第二季度",
          value: "Q2"
        },
        {
          name: "第三季度",
          value: "Q3"
        },
        {
          name: "第四季度",
          value: "Q4"
        }
      ],
      placeholderArr: '        开始季度        ~        结束季度',
      placeholderStr: '选择季度',
      leftYear: "",
      rightYear: "",
      periodDataCopy: [],
      saveClickData: [],
      showChooseBox: false,
      showQuarterly: ""
    }
  },
  watch: {
    periodData: {
      handler(newVal) {
        if (this.isInterval && newVal && newVal.length === 2) {
          this.showQuarterly = `${newVal[0].substr(0, 4)}年第 ${newVal[0].substr(5)} 季度  ~  ${newVal[1].substr(0, 4)}年第 ${newVal[1].substr(5)} 季度`
          this.periodDataCopy = newVal
        } else if (!this.isInterval && newVal && newVal.length === 6 ) {
          this.showQuarterly = `${newVal.substr(0, 4)}年第 ${newVal.substr(5)} 季度`
          this.periodDataCopy = newVal
        }
      },
      immediate: true
    }
  },
  methods: {
    openChooseBox() {
      this.setShowYears()
      this.showChooseBox = true
    },
    setShowYears() {
      if (this.isInterval) {
        if (this.periodData[0] && this.periodData[1]) {
          this.leftYear = this.periodData[0].substr(0, 4)
          this.rightYear =
            this.periodData[1].substr(0, 4) !== this.periodData[0].substr(0, 4)
              ? this.periodData[1].substr(0, 4)
              : parseInt(this.periodData[1].substr(0, 4)) + 1
        } else {
          const year = new Date().getFullYear()
          this.rightYear = year
          this.leftYear = year * 1 - 1
        }
      } else {
        if (this.periodData) {
          this.leftYear = this.periodData.substr(0, 4)
        } else {
          const year = new Date().getFullYear()
          this.leftYear = year
        }
      }
    },
    handlerItemClick(value, name) {
      const { periodDataCopy, isInterval } = this
      if (isInterval) {
        if (periodDataCopy && periodDataCopy.length === 2) {
          this.periodDataCopy = []
          this.saveClickData = []
        }
        if (
          this.saveClickData.length === 1 &&
          value < this.saveClickData[0].value
        ) {
          this.periodDataCopy.unshift(value)
          this.saveClickData.unshift({
            value,
            name
          })
        } else {
          this.periodDataCopy.push(value);
          this.saveClickData.push({
            value,
            name
          })
        }
        if (this.periodDataCopy.length === 2) {
          this.$emit("change", periodDataCopy)
          this.showChooseBox = false
        }
      } else {
        this.$emit("change", value);
        this.showChooseBox = false;
      }
    },
    handlerCancel() {
      this.showChooseBox = false
      const { periodDataCopy, isInterval } = this
      if (isInterval && periodDataCopy && periodDataCopy.length !== 2) {
        this.periodDataCopy = this.periodData
        this.saveClickData = []
      }
    }
  }
}
</script>

<style lang="less" scoped>
.quarterly-picker {
  position: relative;
  width: 145px;
  &.interval {
    width: 290px;
  }
  .mask {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    z-index: 998;
  }

  > input {
    box-sizing: border-box;
    width: 100%;
    height: 32px;
    padding: 0 10px;
    border: 1px solid #cccccc;
    border-radius: 4px;
    outline: none;
    cursor: pointer;
  }

  .right-icon {
    font-size: 14px;
    width: 14px;
    height: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    right: 10px;
    top: 50%;
    margin-top: -7px;
    color: #999999;
  }

  .quarterly-choose-box {
    box-shadow: 0 3px 10px rgba(0, 0, 0, 0.15);
    position: absolute;
    z-index: 999;
    top: 32px;
    width: auto;
    height: 250px;
    border-radius: 4px;
    background-color: #fff;
    display: flex;
    & > div {
      flex: 1;
      width: 115px;
      padding: 15px;
      &:first-child {
        border-right: 1px solid #cccccc;
      }

      .box-year {
        height: 28px;
        margin-bottom: 10px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 16px;
        position: relative;

        > span {
          font-weight: bold;
          height: 28px;
          line-height: 30px;
        }

        button {
          height: 28px;
          width: 28px;
          background-color: #ffffff;
          padding: 0 5px;
          text-align: center;
          cursor: pointer;
          border: 0;
          outline: 0;
          .iconfont {
            color: #999999;
            font-size: 16px;
            width: 16px;
            height: 16px;
          }
          &:first-child {
            transform: rotate(180deg);
          }
          &:hover {
            background-color: #eeeeee;
          }
          &:disabled {
            cursor: not-allowed;
            background-color: #ffffff;
            i {
              color: #eeeeee;
            }
          }
        }
      }

      .choose-box {
        .choose-list {
          .choose-item {
            text-align: center;
            line-height: 40px;
            margin-bottom: 5px;
            font-size: 12px;
            cursor: pointer;
            border-radius: 4px;
            &:hover {
              background-color: #eeeeee;
            }
            &-active {
              background-color: #b6b6b6 !important;
              color: #ffffff !important;
            }
          }
        }
      }
    }
  }
}
</style>
