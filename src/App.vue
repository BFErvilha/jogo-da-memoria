<template>
 <h1>Jogo da Memória</h1>
 <section class="tabuleiro">
   <Carta v-for="(carta, index) in cartaLista" :key="`carta-${index}`" :valor="carta.valor"
   :combinou="carta.combinou"
   :visivel="carta.visivel"
   :posicao="carta.posicao"
   @carta-selecionada="virarCarta"/>
 </section>
 <h2>{{ status }}</h2>
</template>

<script>
import { ref , watch } from 'vue'
import Carta from "./components/Carta"

export default {
  name: 'App',
  components:{
    Carta
  },
  setup(){
    const cartaLista = ref([])
    const usuarioSelecionou = ref ([])
    const status = ref ('')

    for (let i = 0; i < 20; i++){
      cartaLista.value.push({
        valor: i,
        visivel: false,
        posicao: i,
        combinou: false
      })
    }

    const virarCarta = (carta) => {
      cartaLista.value[carta.posicao].visivel = true

      if (usuarioSelecionou.value[0]) {
        usuarioSelecionou.value[1] = carta
        }else {
          usuarioSelecionou.value[0] = carta
        }
      }
    
      watch(usuarioSelecionou,(currentVlue) => {
          if(currentVlue.length == 2){
            const carta1 = currentVlue[0]
            const carta2 = currentVlue[1]

            if(carta1.cartaValor == carta2.cartaValor) {
              status.value = 'Combinou!'

              cartaLista.value[carta1.posicao].combinou = true;
              cartaLista.value[carta2.posicao].combinou = true;

            } else {
              status.value = 'Não combinou!'
              cartaLista.value[carta1.posicao].visivel = false
              cartaLista.value[carta2.posicao].visivel = false
            }
            usuarioSelecionou.value.length = 0
          }
      }, {deep: true})
      return{
        cartaLista,
        virarCarta,
        usuarioSelecionou,
        status

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
