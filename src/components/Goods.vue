<template>
  <div class="wrap">
    <div class="menuBar">
      <div class="container">
      <ul >
      <virtualList :data="menuList" :height="500" ref="vb" style="overflow:scroll">
        <template slot-scope="{ data }">
          <li class="ct"  :key="data.id">
            {{ data.label }}xx
          </li>

        </template>
      </virtualList>
      </ul>
      </div>
    </div>
    <div class="goods"></div>
  </div>
</template>
<script lang='ts'>
//  Inject, Provide, Prop, Model, Watch, Emit, Mixins
import { Vue, Component, Prop } from 'vue-property-decorator'
import virtualList from './virtualList.vue'
@Component({
  components: {
    virtualList
  }
})
export default class Goods extends Vue {
  @Prop() itemProps !: Object;
  menuList = [];
  menuIndex = 0;
  created () {
    for (let i = 0; i < 1000; i++) {
      let obj = { label: '热卖', height: 50, id: i }
      this.menuList.push(obj)
    }
  }
}
</script>
<style scoped lang='scss'>
.wrap {
  display: flex;
  position: relative;
  height: 100vh;
  .menuBar {
    position: absolute;
    left: 0;
    width: 26.67vw;
    top: 0;
    bottom: 0;
    right: 0;
    .container{
      height:133.33vw;
      overflow:scroll;
    }
    ul {
      min-height: 100%;
      background-color: #f7f8fa;
      li {
        width: 26.67vw;
        line-height: 40px;

      }
      .active {
        background-color: #fff;
      }
    }
  }
  .goods {
    position: absolute;
    left: 26.67vw;
    right: 0;
    top: 0;
    bottom: 0;
    flex: 1;
    height: 100px;
  }
}
</style>
