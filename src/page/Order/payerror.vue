<template>
  <div class="w">
    <y-shelf title="支付失败" style="text-align: center">
      <div slot="content" class="content">
        <p>{{errMsg}}</p>
        <div style="margin-top: 20px;">
          <router-link to="/home">
            <y-button text="继续选购" style="width: 150px;height: 50px;line-height: 48px;font-size: 20px"
                      classStyle="main-btn"></y-button>

          </router-link>
        </div>
      </div>
    </y-shelf>
  </div>
</template>
<script>
  import YShelf from '/components/shelf'
  import YButton from '/components/YButton'
  export default {
    data () {
      return {
        errMsg:''
      }
    },
    components: {
      YShelf,
      YButton
    },
    created () {
      this.errMsg = '支付失败，没有可用的支付通道，请联系客服。'
    }
  }
</script>
<style lang="scss" scoped>
  .content {
    padding: 150px 0;
    font-size: 30px;
  }

  .btn {

  }
   span.price {
    color: #d44d44;
    font-weight: 700;
    line-height: 20px;
    text-align: right;
  }
</style>
