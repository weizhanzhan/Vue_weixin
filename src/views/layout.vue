<template>
  <div class="home" ref="touchAll">
    <header-bar :left_type="left_type"></header-bar>
    <div class="content" >
      <transition :name="transitionName">
        <router-view style="width:100%;height:100%" class="Router" ref='router' @touchstart.native='touchStart'  @touchmove.native='touchMove' @touchend.native='touchEnd'
          :style="slideEffect" >
        </router-view>
      </transition>
    </div>
    <nav-bar></nav-bar>
  </div>
</template>

<script>
  import NavBar from '../components/common/navBar'
  import HeaderBar from '../components/common/headerBar'
  import BScroll from 'better-scroll'
  import {
    touchPushRouter
  } from '../utils/touch-routes'
  export default {
    data() {
      return {
        url: "",
        left_type: {
          type: 'left',
          content_type: "word",
          content: "微信（60）"
        },
        transitionName: 'notranstion',
        startX: 0, //开始触摸的位置
        startY: 0,
        moveX: 0, //滑动时的位置
        moveY: 0,
        endX: 0, //结束触摸的位置
        endY: 0,
        disX: 0, //移动距离
        disY: 0,
        slideEffect: '', //滑动时的效果,使用v-bind:style="deleteSlider"
      }
    },
    methods: {
      touchStart: function (ev) {
        // ev = ev || event;  会导致点击事件无法生效
        // ev.preventDefault();
        if (ev.touches.length == 1) { //tounches类数组，等于1时表示此时有只有一只手指在触摸屏幕
          this.startX = ev.touches[0].clientX; // 记录开始位置
          this.startY = ev.touches[0].clientY
        }
      },
      touchMove: function (ev) {
        // ev = ev || event;
        // ev.preventDefault();
        let btnWidth = this.$refs.touchAll.offsetWidth; //$refs 减少获取dom节点的消耗
        let btnImg = this.$refs.router.offsetWidth;
        if (ev.touches.length == 1) {
          //滑动时距离浏览器左侧的距离
          this.moveX = ev.touches[0].clientX;
          this.moveY = ev.touches[0].clientY;
          //实时的滑动的距离-起始位置=实时移动的位置
          this.disX = this.moveX - this.startX;
          this.disY = this.moveY - this.startY;

        }
      },
      touchEnd: function (ev) {
        // ev = ev || event;
        // ev.preventDefault();
        let btnWidth = this.$refs.touchAll.offsetWidth;
        let btnImg = this.$refs.router.offsetWidth;
        if (ev.changedTouches.length == 1) {
          let endX = ev.changedTouches[0].clientX;
          let endY = ev.changedTouches[0].clientY;
          this.disX = endX - this.startX;
          this.disY = endY - this.startY;
          if (this.disY > -30 && this.disY < 30) { //30是上下滑动的幅度
            if (this.disX > 20) { //20是左右滑动的幅度小于的话就不执行
              this.transitionName = 'slide-right'
              touchPushRouter(this.$route.name, -1)
            } else if (this.disX < -20) {
              this.transitionName = 'slide-left'
              touchPushRouter(this.$route.name, 1)
            }
          }
        }
        
      }
      

    },
    watch:{
      '$route'(){
        console.log('change')
        setTimeout(()=>{
          this.transitionName = "notranstion"
        },100)
        
      }
    },
    mounted() {
      // this.$nextTick(() => {
      //     let bscrollDom = this.$refs.list;
      //     this.aBScroll = new BScroll(bscrollDom,{
      //         click:true
      //     })
      // })   
    },
    components: {
      NavBar,
      HeaderBar
    }
  }

</script>

<style scoped>
  .Router {
    position: absolute;
    width: 100%;
    transition: all .5s ease;

  }
  .notranstion-enter-active, .notranstion-leave-active {
  transition: opacity .5s
}
  .notranstion-enter, .notranstion-leave-active {
    opacity: 0
  }

  .slide-left-enter,
  .slide-right-leave-active {
    /* opacity: 0; */
    -webkit-transform: translate(100%, 0);
    transform: translate(100%, 0);
  }

  .slide-left-leave-active,
  .slide-right-enter {
    /* opacity: 0; */
    -webkit-transform: translate(-100%, 0);
    transform: translate(-100% 0);
  }

  .notranstion-enter {
    opacity: 0;
  }

  .notranstion-leave-active {
    opacity: 0;
  }

  .home {
    /* position: relative; */
  }

  .content {
    position: absolute;
    overflow: hidden;
    top: 1.2rem;
    bottom: 1.5rem;
    font-size: .4rem;
    width: 100%;
    /* transition: all .3s ease; */
  }

  .vx_item_img {
    width: 2rem;
    padding: .2rem
  }

</style>
