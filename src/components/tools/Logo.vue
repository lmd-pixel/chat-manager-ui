<template>
  <div class="logo">
    <router-link :to="{name:'Analysis'}">
      <!--<LogoSvg alt="logo" />-->
      <!-- <image :src="require('@/assets/logo.png')" style="height:64px;width:64px;"/> -->
      <h1 v-if="showTitle" style="font-size: 20px;">{{ title }}</h1>
    </router-link>
  </div>
</template>

<script>
import LogoSvg from '@/assets/logo.svg?inline'
export default {
  name: 'Logo',
  components: {
    LogoSvg
  },
  props: {
    title: {
      type: String,
      default: '"余村经验".基层社会治理平台',
      required: false
    },
    showTitle: {
      type: Boolean,
      default: true,
      required: false
    }
  }
}
</script>
