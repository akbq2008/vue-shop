<template>
  <div class="home">
    <cart></cart>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import cart from '@/components/Cart.vue' // @ is an alias to /src

@Component({
  components: {
    cart
  }
})
export default class Home extends Vue { }
</script>
