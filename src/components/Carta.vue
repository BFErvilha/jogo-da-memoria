<template>
  <div class="carta">
    {{valor}}
  </div>
</template>

<script>
export default {
  props:{
    valor: {
      type: Number,
      required: true,
    }
  }

}
</script>

<style>
</style>
