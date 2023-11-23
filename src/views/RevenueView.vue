<template>
  <div class="container mx-auto h-full w-screen">
    <div class="w-full flex h-screen">
      <RevenueMap
        :scale="3700"
        title="Revenue deviation"
        subtitle="Revenue deviation in percentage"
      />
    </div>
  </div>
</template>

<script>
import RevenueMap from '../components/RevenueMap.vue'

export default { components: { RevenueMap } }
</script>

<style lang="scss" scoped></style>
