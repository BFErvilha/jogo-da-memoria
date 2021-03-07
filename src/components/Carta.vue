<template>
  <div class="carta" :class="virarEstilo" @click="selecionarCarta">
    <div class="carta-face frente">
      <img :src="`/assets/img/cartas/${valor}.png`" :alt="valor">
      <img v-if="combinou" src="/assets/img/check-apple.png" alt="Carta Combinada" class="icone-check">
    </div>
    <div class="carta-face costas"></div>
  </div>
</template>

<script>
import { computed } from 'vue'

export default {
  props:{
    valor: {
      type: String,
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
  setup( props , context ){
    const virarEstilo = computed(() => {
      let virada

      if(props.visivel){
        virada = 'foi-virada'
      }

      return virada
    })

    const selecionarCarta = () => {
      context.emit('carta-selecionada', {
        posicao: props.posicao,
        cartaValor: props.valor
      })
    }
    return {
      selecionarCarta,
      virarEstilo
    }
  }
}
</script>

<style>

.carta{
  position: relative;
  cursor: pointer;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;
}

.carta.foi-virada{
  transform: rotateY(180deg)
}

.carta:hover{
  box-shadow: 0 0 11px rgb(255,255,255); 
  border-radius: 10px;
}

.carta-face{
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  backface-visibility: hidden;
}

.carta-face.frente{
  background-color: #172937;
  color: white
}

.carta-face.frente img{
  border-radius: 10px;
  transform: rotateY(180deg);
}
.carta-face.costas{
  background-image: url('../../public/assets/img/carta-verso.png');
  color: white;
}

.icone-check{
  position: absolute;
  right: 5px;
  top: 5px;
}
</style>
