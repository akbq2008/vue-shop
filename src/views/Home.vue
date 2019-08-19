<template>
  <div class="home">
    <!-- <goods></goods> -->
    <!-- <virtual-list
      :size="40"
      :remain="8"
      :item="item"
      :itemcount="100"
      :itemprops="getItemprops"
    /> -->

    <!-- <cart></cart> -->
  <div
    class="list-view"
    @scroll="handleScroll">
    <div
      class="list-view-phantom"
      :style="{
         height: contentHeight
      }">
    </div>
    <div
      ref="content"
      class="list-view-content">
      <div
        class="list-view-item"
        :style="{
          height: itemHeight + 'px'
        }"
        :key="item.id"
        v-for="item in visibleData">
        {{ item.value }}
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import cart from '@/components/Cart.vue' // @ is an alias to /src
import goods from '../components/Goods.vue'
export default {
  name: 'ListView',

  props: {
    // data: {
    //     type: Array,
    //   required: true
    // },

    itemHeight: {
      type: Number,
      default: 30
    },
    itemSizeGetter: {
      type: Function
    }
  },
  computed: {
    contentHeight () {
      return this.dataList.length * this.itemHeight + 'px'
    }
  },

  mounted () {
    for (let i = 0; i < 100; i++) {
      let obj = { value: '热卖' }
      this.dataList.push(obj)
    }
    this.updateVisibleData()
  },

  data () {
    return {
      dataList: [],
      visibleData: []
      // itemSizeGetter
    }
  },

  methods: {
    findNearestItemIndex (scrollTop) {
      const { dataList, itemSizeGetter } = this
      console.log(dataList, itemSizeGetter, 'dataList')

      let total = 0
      for (let i = 0, j = dataList.length; i < j; i++) {
        const size = itemSizeGetter.call(null, dataList[i], i)
        total += size
        if (total >= scrollTop || i === j - 1) {
          return i
        }
      }

      return 0
    },
    getItemSizeAndOffset (index) {
      const { lastMeasuredIndex, sizeAndOffsetCahce, dataList, itemSizeGetter } = this
      if (lastMeasuredIndex >= index) {
        return sizeAndOffsetCahce[index]
      }
      let offset = 0
      if (lastMeasuredIndex >= 0) {
        const lastMeasured = sizeAndOffsetCahce[lastMeasuredIndex]
        if (lastMeasured) {
          offset = lastMeasured.offset + lastMeasured.size
        }
      }
      for (let i = lastMeasuredIndex + 1; i <= index; i++) {
        const item = dataList[i]
        const size = itemSizeGetter.call(null, item, i)
        sizeAndOffsetCahce[i] = {
          size,
          offset
        }
        offset += size
      }
      if (index > lastMeasuredIndex) {
        this.lastMeasuredIndex = index
      }
      return sizeAndOffsetCahce[index]
    },
    updateVisibleData (scrollTop) {
      scrollTop = scrollTop || 0
      const start = this.findNearestItemIndex(scrollTop)
      const end = this.findNearestItemIndex(scrollTop + this.$el.clientHeight)
      this.visibleData = this.dataList.slice(start, Math.min(end + 1, this.data.length))
      this.$refs.content.style.webkitTransform = `translate3d(0, ${this.getItemSizeAndOffset(start).offset}px, 0)`
      // scrollTop = scrollTop || 0
      // const visibleCount = Math.ceil(this.$el.clientHeight / this.itemHeight) // 取得可见区域的可见列表项数量
      // const start = Math.floor(scrollTop / this.itemHeight) // 取得可见区域的起始数据索引
      // const end = start + visibleCount // 取得可见区域的结束数据索引
      // this.visibleData = this.dataList.slice(start, end) // 计算出可见区域对应的数据，让 Vue.js 更新
      // this.$refs.content.style.webkitTransform = `translate3d(0, ${start * this.itemHeight}px, 0)` // 把可见区域的 top 设置为起始元素在整个列表中的位置（使用 transform 是为了更好的性能）
    },

    handleScroll () {
      const scrollTop = this.$el.scrollTop
      this.updateVisibleData(scrollTop)
    }
  }
}
</script>

<style scoped>
.list-view {
  height: 400px;
  overflow: auto;
  position: relative;
  border: 1px solid #aaa;
}

.list-view-phantom {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  z-index: -1;
}

.list-view-content {
  left: 0;
  right: 0;
  top: 0;
  position: absolute;
}

.list-view-item {
  padding: 5px;
  color: #666;
  line-height: 30px;
  box-sizing: border-box;
}
</style>
