<template>
 <h1>Jogo da Memória</h1>
 <section class="tabuleiro">
   <Carta v-for="(carta, index) in cartaLista" :key="`carta-${index}`" :valor="carta.valor"
   :visivel="carta.visivel"
   :posicao="carta.posicao"
   @carta-selecionada="virarCarta"/>
 </section>
</template>

<script>
import { ref } from 'vue'
import Carta from "./components/Carta"

export default {
  name: 'App',
  components:{
    Carta
  },
  setup(){
    const cartaLista = ref([])

    for (let i = 0; i < 20; i++){
      cartaLista.value.push({
        valor: i,
        visivel: false,
        posicao: i
      })
    }

    const virarCarta = (carta) => {
      cartaLista.value[carta.posicao].visivel = true;
    }

    return{
      cartaLista,
      virarCarta
    }

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.tabuleiro{
  display: grid;
  grid-template-columns:  100px 100px 100px 100px 100px;
  grid-template-rows: 150px 150px 150px 150px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}
</style>
