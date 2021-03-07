<template>
  <div class="titulo"> 
    <h1>Abstergo Memory</h1>
  </div>  
 <transition-group tag="section" name="embaralhar-cartas" class="tabuleiro">
   <Carta v-for="carta in cartaLista" :key="`${carta.valor}-${carta.variante}`" :valor="carta.valor"
   :combinou="carta.combinou"
   :visivel="carta.visivel"
   :posicao="carta.posicao"
   @carta-selecionada="virarCarta"/>
 </transition-group>
 <h2>{{ status }}</h2>
 <button @click="reiniciarJogo" class="btn">
   <img src="../public/assets/img/restart.svg" alt="Reiniciar icone"/>
   ReiniciarJogo
 </button>
</template>

<script>
import _ from 'lodash'
import { computed, ref , watch } from 'vue'
import Carta from "./components/Carta"

export default {
  name: 'App',
  components:{
    Carta
  },
  setup(){
    const cartaLista = ref([])
    const usuarioSelecionou = ref ([])
    
    const status = computed(()=> {
      if(paresRestantes.value === 0){
         return 'Parabéns, você venceu!'
      } else {
        return `Pares restantes: ${paresRestantes.value}`
      }
    })

    const paresRestantes = computed(() => {
      const cartasRestantes =  cartaLista.value.filter(carta => carta.combinou === false).length

       return cartasRestantes / 2 
    })

    const reiniciarJogo = () => {
      cartaLista.value = _.shuffle(cartaLista.value)

      cartaLista.value = cartaLista.value.map((carta,index) => {
        return { 
          ...carta,
          combinou: false,
          posicao: index,
          visivel: false
        }
      })
    }

    const cartasItens = [ 'ac' , 'ac-2' , 'ac-3' , 'ac-black-flag-fc' , 'ac-black-flag' , 'ac-chronicles' , 'ac-liberation' , 'ac-revelations' , 'ac-syndicate' , 'ac-unity' ]

    cartasItens.forEach(item => {
      cartaLista.value.push({
        valor: item,
        variante: 1,
        visivel: false,
        posicao: null,
        combinou: false
      })

      cartaLista.value.push({
        valor: item,
        variante: 2,
        visivel: false,
        posicao: null,
        combinou: false
      })
    })

    cartaLista.value = cartaLista.value.map((carta,index) => {
      return { 
        ...carta,
        posicao: index
      }
    })


    const virarCarta = (carta) => {
      cartaLista.value[carta.posicao].visivel = true

      if (usuarioSelecionou.value[0]) {
        if((usuarioSelecionou.value[0].posicao === carta.posicao && usuarioSelecionou.value[0].faceValor )=== carta.faceValor){
          return
        }
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
              cartaLista.value[carta1.posicao].combinou = true;
              cartaLista.value[carta2.posicao].combinou = true;
            } else {
              setTimeout(() => {
                cartaLista.value[carta1.posicao].visivel = false
                cartaLista.value[carta2.posicao].visivel = false
              }, 2000 )
            }
            usuarioSelecionou.value.length = 0
          }
      }, {deep: true})
      return{
        cartaLista,
        virarCarta,
        usuarioSelecionou,
        status,
        reiniciarJogo
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
  color: #fff;
  margin-top: 60px;
  /* height: 100vh; */
}

.titulo h1{
  font-family: 'Assassin$ Regular';
  font-size: 60px;
  margin: 30px auto;

}

.tabuleiro{
  display: grid;
  grid-template-columns: repeat(5, 100px);
  grid-template-rows: repeat(4, 150px);
  grid-column-gap: 25px;
  grid-row-gap: 25px;
  justify-content: center;
}

.btn{
  background-color: #6a9eb8;
  border: 1px solid #6a9eb8;
  border-radius: 10px;
  color: #fff;
  padding: 0.75rem 0.5rem;
  margin: 15px auto;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  cursor: pointer;
}

.btn:hover{
  background-color: #afdcf0;
  border-color: #afdcf0;
}

.btn img{
  padding-right: 5px;
}

.embaralhar-cartas-move{
  transition: transform 0.8s ease-in
}
</style>
