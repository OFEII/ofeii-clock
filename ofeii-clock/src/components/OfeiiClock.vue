<template>
  <!-- container -->
  <div class="container" :class="[isNight ? 'container-night' : 'container-day','container']">
    <!-- clock-day/night -->
    <div
      @click="timeStop()"
      :class="[isNight ? 'clock-night' : 'clock-day','ofeii-clock',(isNight&&isTimeStop)?'stop-night':'',(!isNight&&isTimeStop)?'stop-day':'']"
    >
      <!-- hour时针-->
      <div class="hour">
        <div
          class="hr"
          v-drag="{set:setDigitalHour}"
          draggable="false"
          id="clock-hour"
          :style="{transform: `rotateZ(${hourDeg}deg)`}"
        >
          <span class="tail hr-tail"></span>
        </div>
      </div>
      <!-- min分针 -->
      <div class="min">
        <div
          class="mn"
          v-drag="{set:setDigitalMin}"
          draggable="false"
          id="clock-min"
          :style="{transform: `rotateZ(${minDeg}deg)`}"
        >
          <span class="tail mn-tail"></span>
        </div>
      </div>
      <!-- sec秒针 -->
      <div class="sec">
        <div
          class="sc"
          v-drag="{set:setDigitalSec}"
          draggable="false"
          id="clock-sec"
          :style="{transform: `rotateZ(${secDeg}deg)`}"
        ></div>
      </div>
    </div>
    <!-- digital-clock 电子时钟 -->
    <div class="digital-clock">
      <div class="clock-ctn">
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handlePlusHour()">+</button>
        <div :class="[isNight ? 'digit-night' : 'digit-day','digit']">{{digitalHour}}</div>
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handleSubHour()">-</button>
      </div>
      <div class="clock-ctn">
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handlePlusMin()">+</button>
        <div :class="[isNight ? 'digit-night' : 'digit-day','digit']">{{digitalMin}}</div>
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handleSubMin()">-</button>
      </div>
      <div class="clock-ctn">
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handlePlusSec()">+</button>
        <div :class="[isNight ? 'digit-night' : 'digit-day','digit']">{{digitalSec}}</div>
        <button :class="[isNight ? 'btn-night' : 'btn-day','btn-base']" @click="handleSubSec()">-</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 指针转动角度
      secDeg: 0,
      minDeg: 0,
      hourDeg: 0,
      // 电子时钟(时 分 秒) digital-Time(Hour, Min ,Sec)
      digitalHour: "",
      digitalMin: "",
      digitalSec: "",
      // 控制是否停止时间停止的状态
      isTimeStop: false,
      // 控制夜晚()或者白天的状态
      isNight: false
    };
  },
  created() {
    this.timeRun();
    this.timeInit();
  },
  methods: {
    // 时间初始化:获取本地的时间并将其转为string，并用padStart()补全
    timeInit() {
      const deg = 6;
      let nowTime = new Date();
      // console.log(nowTime.getHours())
      this.digitalHour = (nowTime.getHours() + "").padStart(2, "0");
      this.digitalMin = (nowTime.getMinutes() + "").padStart(2, "0");
      this.digitalSec = (nowTime.getSeconds() + "").padStart(2, "0");
    },
    // 个位数补零
    // zeroPadded(num){
    //   return num < 10 ? `0${num}` : num
    // },
    // 秒针+1,改变指针角度并判断夜晚或白天的状态
    clock() {
      this.digitalSec = (+this.digitalSec + 1 + "").padStart(2, "0");
      this.resetTimeDeg();
      this.isMode();
    },
    // 模拟指针转动:每秒运行一次clock()
    timeRun() {
      this.timerunning = setInterval(() => {
        this.clock();
      }, 1000);
    },
    // 点击时钟表盘进行切换(暂停态 <==> 停止态)
    timeStop() {
      this.isTimeStop = !this.isTimeStop;
      this.isTimeStop ? clearInterval(this.timerunning) : this.timeRun();
    },
    // 重置时间指针转动的角度
    resetTimeDeg() {
      // this.hourDeg =(this.digitalHour / 12) * 360 + (this.digitalMin / 60) * 30;
      // this.minDeg = (this.digitalMin / 60) * 360 + (this.digitalSec / 60) * 6;
      // this.secDeg = (this.digitalSec / 60) * 360;
      this.hourDeg = this.digitalHour * 30 + this.digitalMin / 2;
      this.minDeg = this.digitalMin * 6 + this.digitalSec / 10;
      this.secDeg = this.digitalSec * 6;
      this.formatHour();
      this.formatMin();
      this.formatSec();
    },
    // 时针格式化
    formatHour() {
      if (this.digitalHour >= 24) {
        this.digitalHour = ((+this.digitalHour)%24 +'').padStart(2, "0");
      } else if (this.digitalHour < 0) {
        this.digitalHour = (+this.digitalHour + Math.round(Math.abs(this.digitalHour)/24+1)*24 + "").padStart(2, "0");
      }
    },
    // 分针格式化
    formatMin() {
      if (this.digitalMin >= 60) {
        this.digitalMin = ((+this.digitalMin % 60) + "").padStart(2, "0");
        this.digitalHour = (+this.digitalHour + 1 + "").padStart(2, "0");
      } else if (this.digitalMin < 0) {
        this.digitalMin = (+this.digitalMin + Math.round(Math.abs(this.digitalHour)/60+1)*60 + "").padStart(2, "0");
        this.digitalHour = (+this.digitalHour - 1 + "").padStart(2, "0");
      }
    },
    // 秒针格式化
    formatSec() {
      if (this.digitalSec >= 60) {
        this.digitalSec = ((+this.digitalSec % 60) + "").padStart(2, "0");
        this.digitalMin = (+this.digitalMin + 1 + "").padStart(2, "0");
      } else if (this.digitalSec < 0) {
        this.digitalSec = (+this.digitalSec + Math.round(Math.abs(this.digitalHour)/60+1)*60 + "").padStart(2, "0");
        this.digitalMin = (+this.digitalMin - 1 + "").padStart(2, "0");
      }
    },
    // 时针点击+1
    handlePlusHour() {
      this.digitalHour = (+this.digitalHour + 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 时针点击-1
    handleSubHour() {
      this.digitalHour = (+this.digitalHour - 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 分针点击+1
    handlePlusMin() {
      this.digitalMin = (+this.digitalMin + 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 分针点击-1
    handleSubMin() {
      this.digitalMin = (+this.digitalMin - 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 秒针点击+1
    handlePlusSec() {
      this.digitalSec = (+this.digitalSec + 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 秒针点击-1
    handleSubSec() {
      this.digitalSec = (+this.digitalSec - 1 + "").padStart(2, "0");
      this.resetTimeDeg();
    },
    // 拖拽时针改变电子表盘的digitalHour
    setDigitalHour(deg) {
      this.hourDeg = Math.round(this.hourDeg - deg);
      this.digitalHour = (
        Math.round((this.hourDeg - +this.digitalMin / 2) / 30) + ""
      ).padStart(2, "0");
      this.formatHour();
      this.$forceUpdate();
    },
    // 拖拽分针改变电子表盘的digitalMin
    setDigitalMin(deg) {
      this.minDeg = Math.round(this.minDeg - deg);
      this.digitalMin = (
        Math.round((this.minDeg - +this.digitalSec / 10) / 6) + ""
      ).padStart(2, "0");
      this.digitalHour = (
        Math.round((this.hourDeg - +this.digitalMin / 2) / 30) + ""
      ).padStart(2, "0");
      console.log(this.digitalMin);
      this.formatHour();
      this.formatMin();
      this.$forceUpdate();
    },
    // 拖拽秒针改变电子表盘的digitalSec
    setDigitalSec(deg) {
      this.secDeg = Math.round(this.secDeg - deg);
      this.digitalSec = (Math.round(this.secDeg / 6) + "").padStart(2, "0");
      this.digitalMin = (
        Math.round((this.minDeg - +this.digitalSec / 10) / 6) + ""
      ).padStart(2, "0");
      this.digitalHour = (
        Math.round((this.hourDeg - +this.digitalMin / 2) / 30) + ""
      ).padStart(2, "0");
      this.formatHour();
      this.formatMin();
      this.formatSec();
      this.$forceUpdate();
    },
    // 判断夜晚或白天模式
    isMode() {
      let numDh = +this.digitalHour;
      this.isNight =
        (numDh >= 22 && numDh <= 23) || (numDh >= 0 && numDh <= 6)
          ? true
          : false;
    }
  },
  directives: {
    // 自定义指令 v-drag，el当前元素
    drag(el, binding) {
      // pc端拖拽
      el.onmousedown = function(e) {
        //鼠标按下，计算当前元素距离client的距离
        let disX = e.clientX - el.offsetLeft;
        let disY = e.clientY - el.offsetTop;
        let rect = e.target.getBoundingClientRect();
        let centerX = rect.left + rect.width / 2;
        let centerY = rect.top + rect.width / 2;
        document.onmousemove = function(e) {
          //鼠标移动，计算其移动距离
          let l = e.clientX - centerX - disX;
          let t = centerY - e.clientY - disY;
          // 计算其旋转角度
          let deg = (Math.atan2(t, l) / Math.PI) * 1.5;
          // 旋转
          el.style.transform = `rotateZ(${deg}deg)`;
          // set传出deg给data
          binding.value.set(deg);
        };
        document.onmouseup = function(e) {
          document.onmousemove = null;
          document.onmouseup = null;
        };
        //return false不加的话可能导致黏连，就是拖到一个地方时div粘在鼠标上不下来，相当于onmouseup失效
        return false;
      };
      el.ontouchstart = function(e) {
        //鼠标按下。计算当前元素距离可视区的距离
        let disX = e.clientX - el.offsetLeft;
        let disY = e.clientY - el.offsetTop;
        let rect = e.target.getBoundingClientRect();
        let centerX = rect.left + rect.width / 2;
        let centerY = rect.top + rect.width / 2;
        document.ontouchmove = function(e) {
          //鼠标移动。计算鼠标移动距离
          let l = e.clientX - centerX - disX;
          let t = centerY - e.clientY - disY;
          // antan2计算出拖拽角度
          let deg = (Math.atan2(t, l) / Math.PI) * 1.5;
          // 拖拽旋转
          el.style.transform = `rotateZ(${deg}deg)`;
          // set传出deg
          binding.value.set(deg);
        };
        document.ontouchend = function(e) {
          document.ontouchmove = null;
          document.ontouchend = null;
        };
        return false;
      };
    }
  }
};
</script>

<style lang="scss">
$white: #fff;
$bg-white: #f5f6f7;
$day-black: #333;
$day-white: #f5f6f7;
$day-red: rgb(255, 45, 85);
$day-text: #333;
$dark-red: rgb(255, 68, 58);

$day-text-secondary: #666;
$dark-text: rgb(242, 242, 247);
$dark-text-secondary: #979797;

$shandow-dark: #dfe4ea;
$shadow-tl: -4px -2px 4px 0px;
$shadow-br: 4px 2px 6px 0px;
$shadow: $shadow-tl $white, $shadow-br $shandow-dark;

@mixin hand {
  content: "";
  width: 8px;
  height: 5rem;
  border-radius: 8px;
  z-index: 10;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-color: $bg-white;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem 3rem;
  font-family:'Microsoft YaHei';
}

.container {
  width: 23rem;
  height: 46rem;
  border-radius: 8px;
  flex-direction: column;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: $shadow;
  text-align: center;
  .ofeii-clock {
    height: 18rem;
    width: 18rem;
    display: flex;
    justify-content: center;
    align-items: center;
    background: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAZAAAAGQCAYAAACAvzbMAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMDY3IDc5LjE1Nzc0NywgMjAxNS8wMy8zMC0yMzo0MDo0MiAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTUgKFdpbmRvd3MpIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjQwMkM2NkE1RjNDRjExRTlCQzhGRkZGNzZFRDE0RTFDIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjQwMkM2NkE2RjNDRjExRTlCQzhGRkZGNzZFRDE0RTFDIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6NDAyQzY2QTNGM0NGMTFFOUJDOEZGRkY3NkVEMTRFMUMiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6NDAyQzY2QTRGM0NGMTFFOUJDOEZGRkY3NkVEMTRFMUMiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz6dNzTWAAAT+UlEQVR42uzd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");
    background-size: cover;
    border-radius: 50%;
    cursor: pointer;
    &:before {
      content: "";
      position: absolute;
      height: 0.4rem;
      width: 0.4rem;
      border-radius: 50%;
      z-index: 10000;
    }
    .hour,
    .min,
    .sec {
      position: absolute;
      transform-origin: 50% 50% 0;
    }
    .hr,
    .mn,
    .sc {
      display: flex;
      justify-content: center;
      position: absolute;
      border-radius: 50%;
    }
    .hour,
    .hr {
      height: 10rem;
      width: 6px;
    }
    .min,
    .mn {
      height: 13.5rem;
      width: 5px;
    }
    .sec,
    .sc {
      height: 16rem;
      width: 2px;
    }
    .hr:before {
      @include hand;
      height: 5rem;
      transform-origin: 0% 50% 0;
      z-index: 13;
    }
    .mn:before {
      @include hand;
      height: 7rem;
      width: 4px;
      transform-origin: 0% 50% 0;
      z-index: 12;
    }
    .sc:before {
      @include hand;
      height: 9rem;
      width: 2px;
      z-index: 11;
      transform-origin: 0% 50% 0;
    }
    .tail {
      background: #fff;
      height: 1rem;
      position: absolute;
      top: 2px;
      border-radius: 8px;
      z-index: 100;
    }
    .hr-tail {
      width: 2px;
    }
    .mn-tail {
      width: 1px;
    }
  }
  .digital-clock {
    margin-top: 4rem;
    display: flex;
    justify-content: center;
    font-size: 2.8rem;
    .digit {
      margin: 0 6px;
      height: 5rem;
      width: 4rem;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 4px;
    }
  }
}

.digit-day {
  background-color: $day-white;
  box-shadow: $shadow;
}

.digit-night {
  color: #f2f2f2;
  background-color: #1a1a1a;
  box-shadow: -2px -2px 3px 0 rgba(255, 255, 255, 0.06), 2px 2px 6px 0 black;
}
.btn-base {
  color: $day-text-secondary;
  height: 2rem;
  width: 4rem;
  position: relative;
  border-radius: 4px;
  margin: 1rem 0;
  font-size: 1.5rem;
  background-color: $day-white;
  outline: none;
  border: none;
  box-shadow: $shadow;
  cursor: pointer;
  font-weight: bold;
}

.btn-day {
  color: $day-text-secondary;
  background-color: $day-white;
  box-shadow: $shadow;
}

.btn-day:focus {
  box-shadow: 2px 2px 2px 0px $shandow-dark inset,
    -2px -2px 2px 0px $white inset;
}

.btn-night {
  color: $dark-text-secondary;
  background-color: #1a1a1a;
  box-shadow: -2px -2px 3px 0 rgba(255, 255, 255, 0.06), 2px 2px 6px 0 black;
}

.btn-night:focus {
  box-shadow: 2px 2px 2px 0px #262626 inset, -2px -2px 2px 0px #000 inset;
}

.container-day {
  background: $day-white;
}
.container-night {
  background: #1a1a1a;
  box-shadow: 4px 2px 6px 0px rgba(0, 0, 0, 0.8);
}

.clock-night {
  border: 4px solid #151515;
  box-shadow: 6px 6px 15px rgba(0, 0, 0, 0.91),
    inset 7px 7px 15px rgba(14, 14, 14, 0.82), 4px 8px 15px #060606,
    -1px 0px 8px rgba(255, 255, 255, 0.4);
  &:before {
    background: $dark-text;
    border: 2px solid $dark-red;
  }
  span {
    .tail {
      background: $dark-text;
    }
  }
  .hr:before {
    background: $dark-text;
    box-shadow: -1px 1px 6px 0 rgba(255, 255, 255, 0.4);
  }
  .mn:before {
    background: $dark-text;
    box-shadow: -1px 1px 6px 0 rgba(255, 255, 255, 0.4);
  }
  .sc:before {
    background: $dark-red;
    box-shadow: -1px 1px 6px 0 rgba(255, 255, 255, 0.4);
  }
}
.clock-day {
  background-color: $day-white;
  box-shadow: inset 0.2rem 0.2rem 0.5rem #fff,
    inset -0.2rem -0.2rem 0.5rem rgba(195, 193, 198, 0.9),
    0.3rem 0.3rem 0.5rem rgba(195, 193, 198, 0.9), -0.2rem -0.2rem 0.4rem #fff;
  &:before {
    background: #f3f3f3;
    border: 2px solid $day-red;
  }
  .hr:before {
    background: $day-black;
    box-shadow: -2px 2px 10px 0 rgba(51, 51, 51, 0.4);
  }
  .mn:before {
    background: $day-black;
    box-shadow: -2px 2px 10px 0 rgba(51, 51, 51, 0.4);
  }
  .sc:before {
    background: $day-red;
    box-shadow: -1px 1px 6px 0 rgba(255, 0, 0, 0.4);
  }
}

.stop-night {
  box-shadow: -2px -2px 3px 0 rgba(255, 255, 255, 0.06), 2px 2px 6px 0 black;
}

.stop-day {
  box-shadow: 3px 3px 6px 0px #dfe4ea inset, -3px -3px 6px 0px $dark-text inset;
}
</style>