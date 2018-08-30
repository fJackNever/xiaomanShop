<!--商品详情-->
<template>
  <div class="w store-content">
    <div class="gray-box">
      <div class="gallery-wrapper">
        <div class="gallery">
          <div class="thumbnail">
            <ul>
              <li v-for="(item, i) in small" :key="i" :class="{on:big===item}" @click="big=item">
                <img v-lazy="item" :alt="product.productName">
              </li>
            </ul>
          </div>
          <div class="thumb">
            <div class="big">
              <img :src="big" :alt="product.productName">
            </div>
          </div>
        </div>
      </div>
      <!--右边-->
      <div class="banner">
        <div class="sku-custom-title">
          <h4>{{product.productName}}</h4>
          <h6>
            <span>{{product.sub_title}}</span>
            <span class="price">
              <em>¥</em><i>{{product.salePrice}}</i></span>
          </h6>
        </div>
        <div class="num">
          <span class="params-name">数量</span>
          <buy-num @edit-num="editNum" :limit="Number(product.limit_num)"></buy-num>
        </div>
        <div class="sku-group" v-for="(item, index) in sku" :key="index">
          <span class="sku-title">{{item.title}}</span>
          <div class="sku-wrap">
            <div class="sku-item" :class="{actived: selectSkuId[index] == it.id} " v-for="(it, idx) in item.items" :key="idx" @click="onSelectSku(index, it.id)">
              {{it.name}}
            </div>  
            </div>
        </div>
        
        <div class="buy">
          <y-button text="加入购物车"
                    @btnClick="addCart(product.productId,product.salePrice,product.productName,product.productImageBig)"
                    classStyle="main-btn"
                    style="width: 145px;height: 50px;line-height: 48px"></y-button>
          <y-button text="现在购买"
                    @btnClick="checkout(product.productId)"
                    style="width: 145px;height: 50px;line-height: 48px"></y-button>
        </div>
      </div>
    </div>
    <!--产品信息-->
    <div class="item-info">
      <y-shelf title="产品信息" subTitle="用户评价">
        <div slot="content">
          <div class="img-item" v-if="productMsg.productDetImgs && productMsg.productDetImgs.length">
            <img v-for="(item, i) in productMsg.productDetImgs"
                 v-if="item"
                 v-lazy="item"
                 alt="产品信息"
                 :key="i"
                >
          </div>
          <div class="no-info" v-else>
            该产品暂无内容
          </div>
        </div>
        <div slot="comment">
          <div class="no-info">
            该产品暂无评价
          </div>
        </div>
      </y-shelf>
    </div>
  </div>
</template>
<script>
  import { productDet, addCart } from '/api/goods'
  import { mapMutations, mapState } from 'vuex'
  import YShelf from '/components/shelf'
  import BuyNum from '/components/buynum'
  import YButton from '/components/YButton'
  export default {
    data () {
      return {
        productMsg: {},
        small: [],
        big: '',
        product: {},
        productNum: 1,
        sku:[],
        selectSkuId:[1, 5],
      }
    },
    computed: {
      ...mapState(['login', 'showMoveImg', 'showCart'])
    },
    methods: {
      ...mapMutations(['ADD_CART', 'ADD_ANIMATION', 'SHOW_CART']),
      _productDet (productId) {
        productDet({productId}).then(res => {
          let result = res.result
          this.product = result
          this.productMsg = result.productMsg || ''
          this.small = result.productImageSmall.slice(0, 5)
          this.big = this.small[0]
          this._getSku();
        })
      },
       _getSku() {
        let sku = [];
        if (this.product.productKey=='shoes' || this.product.productKey=='boots') {
          sku = [{
          id: 1,
          title: '选择尺码',
          items: [{
            id: 1,
            name: '36'
          },
          {
            id: 2,
            name: '37'
          },
          {
            id: 3,
            name: '38'
          },
          {
            id: 4,
            name: '39'
          },
          {
            id: 9,
            name: '40'
          },
          {
            id: 10,
            name: '41'
          },
          {
            id: 11,
            name: '42'
          },
          {
            id: 12,
            name: '43'
          }]
        },
        {
          id: 2,
          title: '选择颜色',
          items: [{
            id: 5,
            name: '红色'
          },
          {
            id: 6,
            name: '黄色'
          },
          {
            id: 7,
            name: '紫色'
          },
          {
            id: 8,
            name: '黑色'
          }]
        }];
        } else {
         sku = [{
          title: '选择尺码',
          items: [{
            id: 1,
            name: 'M'
          },
          {
            id: 2,
            name: 'L'
          },
          {
            id: 3,
            name: 'XL'
          },
          {
            id: 4,
            name: 'XXL'
          }]
        },
        {
          title: '选择颜色',
          items: [{
            id: 5,
            name: '红色'
          },
          {
            id: 6,
            name: '黄色'
          },
          {
            id: 7,
            name: '紫色'
          },
          {
            id: 8,
            name: '黑色'
          }]
        }];
        }
        this.sku = sku;
      },
      addCart (id, price, name, img) {
        if(this.selectSkuId.length == 0) {
          alert('请选择尺码和颜色')
        }else{
          if (!this.showMoveImg) {     // 动画是否在运动
          if (this.login) { // 登录了 直接存在用户名下
            addCart({productId: id, productNum: this.productNum}).then(res => {
              // 并不重新请求数据
              this.ADD_CART({
                productId: id,
                productPrice: price,
                productName: name,
                productImg: img,
                productNum: this.productNum
              })
            })
          } else { // 未登录 vuex
            this.ADD_CART({
              productId: id,
              productPrice: price,
              productName: name,
              productImg: img,
              productNum: this.productNum
            })
          }
          // 加入购物车动画
          var dom = event.target
          // 获取点击的坐标
          let elLeft = dom.getBoundingClientRect().left + (dom.offsetWidth / 2)
          let elTop = dom.getBoundingClientRect().top + (dom.offsetHeight / 2)
          // 需要触发
          this.ADD_ANIMATION({moveShow: true, elLeft: elLeft, elTop: elTop, img: img})
          if (!this.showCart) {
            this.SHOW_CART({showCart: true})
          }
        }
        }
      },
      checkout (productId) {
        if(this.selectSkuId.length == 0) {
          alert('请选择尺码和颜色')
        } else {
          this.$router.push({path: '/checkout', query: {productId, num: this.productNum}})
        }
      },
      editNum (num) {
        this.productNum = num
      },
      onSelectSku(index, id) {
        this.selectSkuId[index] = id;
        this.selectSkuId = Object.assign([], this.selectSkuId);
      }
    },
    components: {
      YShelf, BuyNum, YButton
    },
    created () {
      let id = this.$route.query.productId
      this._productDet(id)
    }
  }
</script>
<style lang="scss" scoped>
  @import "../../assets/style/mixin";
  .sku-group {
    width: 100%;
    padding: 10px;
    .sku-title{
      color: #8d8d8d;
    }
    .sku-wrap {
      display: flex;
      flex-flow: row wrap;
    }
    .actived {
      border: 1px solid #5c81e3 !important;
      color: #5c81e3;
    }
    .sku-item {
      height: 45px;
      border: 1px solid #e1e1e1;
      margin-right: 10px;
      margin-top: 10px;
      border-radius: 4px;
      line-height: 45px;
      padding: 0 20px;
      cursor: pointer;
    }
  }
  .store-content {
    clear: both;
    width: 1220px;
    min-height: 600px;
    padding: 0 0 25px;
    margin: 0 auto;
  }

  .gray-box {
    display: flex;
    padding: 60px;
    margin: 20px 0;
    .gallery-wrapper {
      .gallery {
        display: flex;
        width: 540px;
        .thumbnail {
          li:first-child {
            margin-top: 0px;
          }
          li {
            @include wh(80px, 95px);
            margin-top: 10px;
            padding: 12px;
            border: 1px solid rgba(0, 0, 0, .06);
            border-radius: 5px;
            cursor: pointer;
            &.on {
              padding: 10px;
              border: 3px solid rgba(0, 0, 0, .2);
            }
            img {
              display: block;
              @include wh(100%);
            }
          }
        }
        .thumb {
          .big {
            margin-left: 20px;
          }
          img {
            display: block;
            @include wh(380px, 513px)
          }
        }
      }
    }
    // 右边
    .banner {
      width: 450px;
      margin-left: 10px;
      h4 {
        font-size: 24px;
        line-height: 1.25;
        color: #000;
        margin-bottom: 13px;
      }
      h6 {
        font-size: 14px;
        line-height: 1.5;
        color: #bdbdbd;
        display: flex;
        align-items: center;
        justify-content: space-between;
      }
      .sku-custom-title {
        overflow: hidden;
        padding: 8px 8px 18px 10px;
        position: relative;
      }
      .params-name {
        padding-right: 20px;
        font-size: 14px;
        color: #8d8d8d;
        line-height: 36px;
      }
      .num {
        padding: 29px 0 8px 10px;
        border-top: 1px solid #ebebeb;
        display: flex;
        align-items: center;
      }
      .buy {
        position: relative;
        border-top: 1px solid #ebebeb;
        padding: 30px 0 0 10px;
      }
    }
  }

  .item-info {

    .gray-box {
      padding: 0;
      display: block;
    }
    .img-item {
      width: 1220px;
      img {
        width: 100%;
        height: auto;
        display: block;
      }
    }
  }

  .no-info {
    padding: 200px 0;
    text-align: center;
    font-size: 30px;
  }

  .price {
    display: block;
    color: #d44d44;
    font-weight: 700;
    font-size: 16px;
    line-height: 20px;
    text-align: right;
    i {
      padding-left: 2px;
      font-size: 24px;
    }
  }
</style>
