<template>
    <el-container style="margin-left: 226px">
      <mainheader></mainheader><span style="width: 0px;height: 0px;display: none">{{screen}}</span>
        <el-main class="main-back">
          <!--消息弹窗-->
          <transition>

            <router-view>

            </router-view>

          </transition>

        </el-main>
    </el-container>
  <!--消息弹窗-->
  <!--<dialog-msg :title="diaglogMsg" :msgType="0" :msgData="diagloMsg" @getAll="parkinMsg"  v-if="dialogVisible">

  </dialog-msg>-->

</template>

<script>
  import store from '../../store/index.js'
  import mainheader from '@/components/header/main-header'  /*header*/
  import sidebar from '@/components/modules/sidebar'  /*header*/


  export default {
    name: 'index',
    data () {
      return {
        screenWidth:0,
        timer:null,
        sotoWhith:null
      }
    },
    mounted () {
      const that = this
      window.onresize = () => {
        return (() => {
          window.screenWidth = document.body.clientWidth
          that.screenWidth = window.screenWidth
        })()
      }


    },
    watch: {
      screenWidth (val) {
        if (!this.timer) {
          this.screenWidth = val
          this.timer = true
          let that = this
          setTimeout(function () {
            // that.screenWidth = that.$store.state.canvasWidth
            this.sotoWhith = that.screenWidth;
            // that.init()
            that.timer = false
          }, 400)
        }
      }
    },
    computed:{
      screen(){
        store.state.vuexWidth= this.screenWidth
        return this.screenWidth
      }
    },
    methods: {

    },
    components:{sidebar,mainheader}
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .v-enter{
    opacity: 0;
    height: 100vh;
    transform: translateX(100vw);
  }
  .v-leave-to{
    opacity: 0;
    height: 100vh;
    /*position: absolute;*/
    transform: translateX(-100vw);
  }
  .v-enter-active,
  .v-leave-active{
    transition: all 0.5s ease;
  }
  h1, h2 {
    font-weight: normal;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }
  .el-container{
    flex-flow: row;
    flex-flow: column;
    width: 100%;
  }
  .main-back{
    background: #f6f7fb;
  }
  .el-main{
    padding: 0px;
  }
</style>
