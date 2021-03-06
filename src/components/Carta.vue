<template>
  <div class="carta" @click="selecionarCarta">
    <div v-if="visivel" class="carta-face frente">
      {{valor}} - {{combinou}}
    </div>
    <div v-else class="carta-face costas">
      costas
    </div>
  </div>
</template>

<script>
export default {
  props:{
    valor: {
      type: Number,
      required: true,
    },
    visivel: {
      type: Boolean,
      default: false,
    },
    posicao:{
      type: Number,
      required: true,
    },
    combinou: {
      type: Boolean,
      default: false,
    }
  },
  setup( props , context){
    const selecionarCarta = () => {
      context.emit('carta-selecionada', {
        posicao: props.posicao,
        cartaValor: props.valor
      })
    }
    return {
      selecionarCarta
    }
  }
}
</script>

<style>

.carta{
  border: 5px solid #c3c4cc;
  position: relative;
}

.carta-face{
  width: 100%;
  height: 100%;
  position: absolute;
}

.carta-face.frente{
  background-color: red;
  color: white
}
.carta-face.costas{
  background-color: blue;
  color: white
}
</style>
