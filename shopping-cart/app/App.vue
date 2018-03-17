<template>
  <div>
    <div v-if="product">
      <!-- <navigation /> -->
      <div>
        <div>
          <button @click="show('product')">商品</button>
          <button @click="show('cart')">购物车({{cart.products.length}})</button>
        </div>
      </div>
      <!-- <product /> -->
      <div class="product"
           v-show="'product'===currentShow">
        <div class="product--left">
          <img :src="productImageUrl" />
        </div>
        <div class="product--right">
          <div class="product__name">
            <h2>{{product.name}}</h2>
          </div>
          <div class="product__description">
            <label class="product__label">描述：</label>
            <span class="product__content">{{product.description}}</span>
          </div>
          <div class="product__price">
            <label class="product__label">价格：</label>
            <span class="product__content">￥{{comboPrice}}</span>
          </div>
          <div class="product__colors">
            <label class="product__label">外观：</label>
            <label class="product__colors__item"
                   v-for="(color,index) of product.colors"
                   :class="{'product__colors__item--selected':color.value===product.currentColor}"
                   @click="select('currentColor',color.value)"
                   :key="index">
              {{color.value}}
            </label>
          </div>
          <div class="product__storages">
            <label class="product__label">存储：</label>
            <label class="product__storages__item"
                   v-for="(storage,index) of product.storages"
                   :class="{'product__storages__item--selected':storage.value===product.currentStorage}"
                   @click="select('currentStorage',storage.value)"
                   :key="index">
              {{storage.value}}
            </label>
          </div>
          <div class="product__button-wrap">
            <button class="btn-add-to-cart"
                    @click="addToCart">加入购物车</button>
          </div>
        </div>
      </div>
      <!-- <cart /> -->
      <div class="cart"
           v-show="'cart'===currentShow">
        <h2>购物车</h2>
        <div class="total-price">
          总价:{{totalPrice}}
        </div>
        <div>
          <div v-for="(item,index) of cart.products"
               :key="index">
            <span>颜色:{{item.currentColor}}</span>
            <span>存储:{{item.currentStorage}}</span>
            <button @click="removeProduct(index)">移除</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script> 
export default {
  el: '#root',
  computed: {
    // 组合价格
    comboPrice () {
      let price = this.product.price
      this.product.colors.forEach(n => {
        if (n.value === this.product.currentColor) {
          price += n.priceOffset
        }
      })
      this.product.storages.forEach(n => {
        if (n.value === this.product.currentStorage) {
          price += n.priceOffset
        }
      })
      this.product.comboPrice = price
      return price
    },
    // 选中颜色的图片地址
    productImageUrl () {
      let url = ''
      this.product.colors.forEach(n => {
        if (n.value === this.product.currentColor) {
          url = n.imageUrl
        }
      })
      return url
    },
    // 计算所有加入到购物车中的总价
    totalPrice () {
      let price = 0
      this.cart.products.forEach(n => {
        price += n.comboPrice
      })
      // 将总价同步到 购物车信息上
      this.cart.totalPrice = price
      return price
    }
  },
  methods: {
    select (attribute, value) {
      this.product[attribute] = value
    },
    ajax () {
      const promise = new Promise(resolve => {
        setTimeout(() => {
          resolve()
        }, 1000)
      })
      return promise
    },
    addToCart () {
      this.cart.products.push({ ...this.product })
    },
    removeProduct (index) {
      this.cart.products.splice(index, 1)
    },
    show (name) {
      this.currentShow = name
    }
  },
  created () {
    this.ajax().then(() => {
      this.product = {
        id: 'iphone6S',
        name: 'Apple/苹果 iPhone 6S',
        price: 5999,
        comboPrice: null, // 组合价需要计算 null 代表未计算
        description: '3D Touch、1200万像素照片、4k视频，强大功能于一身。',
        currentColor: '银色',
        currentStorage: '32G',
        colors: [
          { value: '银色', priceOffset: 0, imageUrl: 'http://o8yu724qs.bkt.clouddn.com/iphone6s-silver-select-2015.png' },
          { value: '深空灰色', priceOffset: 500, imageUrl: 'http://o8yu724qs.bkt.clouddn.com/iphone6s-gray-select-2015.png' },
          { value: '金色', priceOffset: 700, imageUrl: 'http://o8yu724qs.bkt.clouddn.com/iphone6s-gold-select-2015.png' },
          { value: '玫瑰金色', priceOffset: 900, imageUrl: 'http://o8yu724qs.bkt.clouddn.com/iphone6s-rosegold-select-2015.png' }
        ],
        storages: [
          { value: '32G', priceOffset: 0 },
          { value: '64G', priceOffset: 1000 },
          { value: '128G', priceOffset: 2000 }
        ]
      }
    })
  },

  data () {
    return {
      currentShow: 'product',
      cart: { products: [], totalPrice: 0 },
      product: null
    }
  }
}
</script>

<style scoped>
#root {
  color: #2c3e50;
  margin-top: 60px;
  width: 1000px;
  margin: 0 auto;
  overflow: hidden;
}
/* product */
.product--left {
  float: left;
}
.product--right {
  margin-top: 50px;
  float: right;
}
.product--right > div {
  margin: 20px 0;
}
.product__price .product__content {
  font-weight: bold;
  color: #c0392b;
}
.product__colors__item,
.product__storages__item {
  user-select: none;
  text-align: center;
  padding: 5px;
  margin: 0px 8px;
  cursor: pointer;
}
.product__colors__item--selected,
.product__storages__item--selected {
  border: 2px solid #c0392b;
}
.btn-add-to-cart {
  color: #fff;
  background-color: #d9534f;
  border-color: #d43f3a;
  padding: 6px 12px;
  margin-bottom: 0;
  font-size: 14px;
  font-weight: 400;
  line-height: 1.42857143;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  user-select: none;
  background-image: none;
  border: 1px solid transparent;
  border-radius: 4px;
}
/* cart */
.cart {
  clear: both;
}
</style>
