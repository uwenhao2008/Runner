<template>
  {{clock}}
</template>

<script type="text/babel">

  /**
   * 将数字转换为字符串.如果数字是一位数,则在前面补一个零.
   * @param number
   * @returns {string}
   */
  function zero( number ) {
    let numberStr = String( number );
    if ( number < 10 ) {
      numberStr = '0' + numberStr;
    }
    return numberStr;
  }

  export default {
    data() {
      return {
        min: 0,
        sec: 0
      };
    },
    computed: {
      clock() {
        return zero( this.min ) + ':' + zero( this.sec );
      }
    },
    props: {
      type: {
        type: String,
        default: 'countdown'
      },
      auto: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      /**
       * 设定初始时间并开始计时
       * @param {Number} min - 初始分钟数
       * @param {Number} [sec] - 初始秒数
       */
      start( min = 0, sec = 0 ) {
        this.min = min;
        this.sec = sec;
        this.pause();
        this.continue();
      },

      /**
       * 暂停计时
       */
      pause() {
        clearInterval( this.$_timeId );
        this.$_timeId = null;
      },

      /**
       * 倒计时
       */
      continueCountdown() {
        this.$_timeId = setInterval( ()=> {
          this.sec -= 1;
          if ( this.sec === -1 ) {
            this.min -= 1;
            if ( this.min === -1 ) {
              this.stop();
              this.$emit( 'end' );
            } else {
              this.sec = 59;
            }
          }
        }, 1000 );
      },

      /**
       * "正"计时
       */
      continueCountup() {
        this.$_timeId = setInterval( ()=> {
          this.sec += 1;
          if ( this.sec === 60 ) {
            this.min += 1;
            this.sec = 0;
          }
        }, 1000 );
      },

      /**
       * 继续计时
       */
      continue() {
        if ( this.$_timeId ) { return; }
        if ( this.type === 'countdown' ) {
          this.continueCountdown();
        } else {
          this.continueCountup();
        }
      },

      /**
       * 停止计时
       */
      stop() {
        this.pause();
        this.min = 0;
        this.sec = 0;
      }
    },
    ready() {
      if ( this.auto ) {
        this.start();
      }
    },
    beforeDestroy() {
      this.pause();
    }
  };
</script>
