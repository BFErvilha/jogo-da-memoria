<template>
 <h1>Jogo da Memória</h1>
 <section class="tabuleiro">
   <Carta v-for="(carta, index) in cartaLista" :key="`carta-${index}`" :valor="carta"/>
 </section>
</template>

<script>
import Carta from "./components/Carta"

export default {
  name: 'App',
  components:{
    Carta
  },
  setup(){
    const cartaLista = []

    for (let i = 0; i < 20; i++){
      cartaLista.push(i)
    }

    return{
      cartaLista
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

.carta{
  border: 5px solid #c3c4cc
}

.tabuleiro{
  display: grid;
  grid-template-columns:  100px 100px 100px 100px;
  grid-template-rows: 150px 150px 150px 150px 150px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}
</style>
