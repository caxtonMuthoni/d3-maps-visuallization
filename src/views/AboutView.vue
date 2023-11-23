<template>
  <div class="container mx-auto h-full w-screen">
    <div class="w-full flex h-screen">
      <CountyFinancing
        :scale="3700"
        title="County Financing"
        subtitle="Financing in %, (OSR collection by a county divided by the exchequer releases multiplied by 100)"
      />
    </div>
  </div>
</template>

<script>
import CountyFinancing from '../components/CountyFinancing.vue'

export default { components: { CountyFinancing } }
</script>

<style lang="scss" scoped></style>
