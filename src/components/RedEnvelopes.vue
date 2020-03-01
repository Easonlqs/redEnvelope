<template>
  <div class="box">
  <div class="center" v-show="show">
    <h1>红包模拟器</h1>
    <div class="inp">
      <span>红包个数：</span>
      <input v-model="num" type="number">
      <span>个</span>
    </div>
    <div class="inp">
      <span>红包总额：</span>
      <input v-model="total" type="number">
      <span>元</span>
    </div>
    <button class="btn" @click="showModal">点击</button>
  </div>
  <div class="content" v-show="!show">
    <div class="modal">
      <div class="info">
        <img src="../assets/avatar.png" width="70px" height="70px">
        <div v-show="open">
          <h3>幸运儿</h3>
          <h4>给您发送了一个红包</h4>
          <h4>恭喜发财 大吉大利</h4>
        </div>
      </div>
      <div ref="slide" class="slide"></div>
      <button v-show="open" class="play" @click="onClick">点击</button>
      <div class="close" @click="onClose">
        <p>X</p>
      </div>
      <div class="resultWrapper" v-show="!open">
        <h2>{{total}}</h2>
        <h5>红包领取榜</h5>
        <div class="result">
          <p v-for="(val,i) in valueList" :key="i">
            <span>用户{{i+1}}：{{val}} </span>
            <span class="special" v-show="max === val">手气最佳</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
export default {
  name: 'RedEnvelopes',
  data () {
    return {
      show: true,
      num: 1,
      total: 0,
      open: true,
      max: 0,
      valueList: []
    }
  },
  methods: {
    showModal () {
      const parnt = /^[0-9]*[1-9][0-9]*$/
      if (!parnt.test(this.num)) {
        alert('红包个数只能为整数')
        this.num = 1
      } else if (this.total <= 0) {
        alert('红包总额要大于0')
      } else {
        this.open = true
        this.show = false
        this.max = 0
        this.valueList = []
      }
    },
    onClick () {
      this.valueList = this.calculate(this.total, 0.01, this.total / 2, this.num)
      this.$refs.slide.style.bottom = '370px'
      this.open = false
    },
    calculate (total, min, max, length) {
      if (length === 1) {
        this.max = total
        return [total]
      }
      const result = []
      let restValue = total
      let restLength = length
      for (let i = 0; i < length - 1; i++) {
        restLength--
        const restMin = restLength * min
        const restMax = restLength * max
        const usable = restValue - restMin
        const minValue = Math.max(min, restValue - restMax)
        const limit = Math.min(usable - minValue, (max - minValue) * 2)
        result[i] = Math.min(max, minValue + Math.floor(limit * Math.random()))
        if (result[i] >= this.max) {
          this.max = result[i]
        }
        restValue -= result[i]
      }
      result[length - 1] = restValue.toFixed(2)
      if (result[length - 1] >= this.max) {
        this.max = result[length - 1]
      }
      return result
    },
    onClose () {
      this.show = true
      this.$refs.slide.style.bottom = '150px'
    }
  }
}
</script>
<style lang="less" scoped>
.box{
  .center{
    margin: 0 auto;
    .inp{
      margin: 5px;
      input{
        margin: 0 10px;
      }
    }
    .btn{
      display: block;
      width: 200px;
      margin: 0 auto;
      margin-top: 25px;
      cursor: pointer;
    }
  }
  .content{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(143,143,143,0.5);
    text-align: center;
    .modal{
      width: 300px;
      height: 450px;
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      background-color: #B22222;
      opacity: 1;
      border-radius: 10px;
      overflow: hidden;
      .info {
        position: relative;
        top: 40px;
        color: white;
        img {
          border-radius: 35px;
        }
        div {
          font-size: 15px;
        }
        h4:nth-of-type(1) {
          color: grey;
        }
      }
      .slide {
        position: absolute;
        width: 100%;
        bottom: 150px;
        height: 600px;
        border-radius: 70px;
        background-color: #EE6A50;
        z-index: -1;
        transition: bottom 0.4s;
      }
      .play {
          width: 80px;
          height: 80px;
          cursor: pointer;
          border-radius: 40px;
          background-color: orange;
          position: relative;
          top: 40px;
          animation: myrotate 1s infinite;
        }
      @keyframes myrotate {
        0% {
          transform: rotateY(0deg)
        }
        100% {
          transform: rotateY(360deg)
        }
      }
      .close {
        position: absolute;
        top: 10px;
        right: 20px;
        width: 20px;
        height: 20px;
        font-size: 15px;
        color: white;
        background-color: #EE6A50;
      }
      .resultWrapper {
        position: relative;
        top: 50px;
        color: white;
        .result {
          position: relative;
          left: 50px;
          width: 200px;
          height: 180px;
          font-size: 10px;
          background-color: rgba(238,121,66,0.5);
          border-radius: 10px;
          text-align: left;
          box-sizing: border-box;
          padding-left: 10px;
          overflow: auto;
          .special{
            float: right;
            margin-right: 10px;
          }
        }
      }
    }
  }
}
</style>
