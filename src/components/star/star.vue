<template>
  <!--星级评价组件-->
  <div class="star">
    <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item"
          :style="styleObj" track-by="$index"></span>
  </div>
</template>

<script type="text/ecmascript-6">
  const LENGTH = 5; // 亮暗一共5颗星
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';

  export default{
    props: {
      size: { // 每一颗星星的宽高
        type: Number
      },
      space: {  // 星星的间距
        type: Number
      },
      score: {  // 星星亮暗对应的得分
        type: Number
      }
    },
    data() {
      return {
      	styleObj: {
          width: this.size + 'rem',
          height: this.size + 'rem',
          backgroundSize: this.size + 'rem ' + this.size + 'rem',
          backgroundRepeat: 'no-repeat',
          marginLeft: (this.space / 2) + 'rem',
          marginRight: (this.space / 2) + 'rem'
        }
      };
    },
    computed: {
      itemClasses() {
      	// 亮星星规则:0.5分为一个步长，例如:1.1->1on, 1.5->1on+1half, 2.0->2on
        let result = [];
        let score = Math.floor(this.score * 2) / 2;
        let hasDecimal = score % 1 !== 0;
        let integer = Math.floor(score);
        for (let i = 0; i < integer; i++) {
          result.push(CLS_ON);
        }
        if (hasDecimal) {
          result.push(CLS_HALF);
        }
        while (result.length < LENGTH) {
          result.push(CLS_OFF);
        }
        return result;
      }
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .star{
    font-size: 0.0rem;
    .star-item {
      display: inline-block;
      &.on {
        background-image: url("star48_on@3x.png");
      }
      &.off {
        background-image: url("star48_off@3x.png");
      }
      &.half {
        background-image: url("star48_half@3x.png");
      }
    }
  }
</style>






















