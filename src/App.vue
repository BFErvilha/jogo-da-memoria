<template>
  <div class="titulo"> 
    <img src="@/assets/img/abstergo_logo.png" alt="Abstergo"/>
    <h1>Abstergo's Memory</h1>
  </div>
  <section class="placar">
    <div class="nome">
      <h2>{{ jogador }}</h2>
    </div>
    <div>
        <button v-if="novoJogador" @click="iniciarJogo" class="btn">
          <img src="../public/assets/img/play.svg" alt="Reiniciar icone"/>
          Iniciar Jogo
        </button>
        <button v-else @click="reiniciar" class="btn">
          <img src="../public/assets/img/restart.svg" alt="Reiniciar icone"/>
          ReiniciarJogo
        </button>
    </div>
    <div class="contador">
        Tempo : {{ contador }}
         Rodadas: {{ rodada }}
    </div>
  </section>  
 <transition-group tag="section" name="embaralhar-cartas" class="tabuleiro">
   <Carta v-for="carta in cartaLista" :key="`${carta.valor}-${carta.variante}`" :valor="carta.valor"
   :combinou="carta.combinou"
   :visivel="carta.visivel"
   :posicao="carta.posicao"
   @carta-selecionada="virarCarta"/>
 </transition-group>
 <h2 class="status">{{ status }}</h2>
 <teleport to="#modal-inicio">
   <div v-if="showMessage"> 
      <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-container">

              <div class="modal-header">
                <h3>Abstergo's Memory</h3>
                <div class="subtitulo">
                  <p>Bem-vindo ao jogo da memória!</p>
                  <p>Tematizado com o jogo da ubisoft Assassin's Creed</p>  
                </div> 
              </div>

              <div class="modal-body">
                <slot name="body">
                  <input v-model="jogador" label="Nome do Jogador" placeholder="Digite seu nome">
                </slot>
              </div>

              <div class="modal-footer">
                <slot name="footer">
                  <button class="btn" @click="fecharModal">
                    Continuar
                  </button>
                </slot>
              </div>
            </div>
          </div>
        </div>
      </transition>  
   </div>
 </teleport>
 
 <teleport to="#modal-placar">
   <div v-if="finalJogo"> 
      <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-container">

              <div class="modal-header">
                <h3>"Nothing is true, everything is permitted."</h3>
              </div>

              <div class="modal-body">
                <slot name="body">
                    <p><span>{{ jogador }}</span>, parabéns!</p>
                    <p>Você venceu o jogo em <span>{{ rodada }}</span> rodadas e em <span>{{ tempoFinal }}</span> segundos.</p>
                </slot>
              </div>

              <div class="modal-footer">
                <slot name="footer">
                  <button class="btn" @click="jogarNovamente">
                    Jogar Novamente
                  </button>
                  
                  <button class="btn" @click="outroJogador">
                    Novo jogador
                  </button>
                </slot>
              </div>
            </div>
          </div>
        </div>
      </transition>  
   </div>
 </teleport>
 
</template>

<script>
import _ from 'lodash'
import { computed, ref , watch } from 'vue'
import { soltarConfete } from './utilities/confete'
import Carta from "./components/Carta"

export default {
  name: 'App',
  components:{
    Carta,
  },
  data(){
    return{ 
      
    }
  },
  setup(){
    const cartaLista = ref([])
    const usuarioSelecionou = ref ([])
    const novoJogador = ref(true)
    const showMessage = ref(true)
    const finalJogo = ref(false)
    const jogador = ref()

    let contador = ref (0)
    let rodada = ref (0)
    let tempoFinal = ref(0)

    const contarTempo = () => {
      setInterval(() => {
        contador.value +=1        
      }, 1000 )
    }

    const jogarNovamente = () => {
      finalJogo.value = false
      clearInterval()
      contador.value = 0
      rodada.value = 0
      reiniciar()
      setTimeout(() => {
        contarTempo()         
      }, 1000 )
    }

    const outroJogador = () => {
      finalJogo.value = false
      contador.value = 0
      rodada.value = 0

      cartaLista.value.visivel = false

      setTimeout(() => { 
        novoJogador.value = true
        showMessage.value = true         
      }, 500 )
    }

    const iniciarJogo = () => {
      novoJogador.value = false
      reiniciarJogo()
      setTimeout(() => {
        contarTempo()         
      }, 1000 )
    }

    const reiniciar = () => {
      contador.value = 0
      rodada.value = 0

      reiniciarJogo()
    }
    const fecharModal = () => {
      showMessage.value = false
      jogador.value = this.nomeJogador
    }

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
        visivel: true,
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

      watch(paresRestantes, currentValue => {
        if(currentValue === 0){
          soltarConfete()
          setTimeout(() => {
           finalJogo.value = true  
           tempoFinal.value = contador.value
          }, 1000 )
        }
      })
    
      watch(usuarioSelecionou,(currentValue) => {
          if(currentValue.length == 2){
            const carta1 = currentValue[0]
            const carta2 = currentValue[1]

            if(carta1.cartaValor == carta2.cartaValor) {
              cartaLista.value[carta1.posicao].combinou = true;
              cartaLista.value[carta2.posicao].combinou = true;
              rodada.value += 1
            } else {
              setTimeout(() => {
                cartaLista.value[carta1.posicao].visivel = false
                cartaLista.value[carta2.posicao].visivel = false
              }, 2000 )

              rodada.value += 1
            }
            usuarioSelecionou.value.length = 0
          }
      }, {deep: true})
      return{
        cartaLista,
        virarCarta,
        usuarioSelecionou,
        status,
        reiniciarJogo,
        iniciarJogo,
        novoJogador,
        fecharModal,
        showMessage,
        contarTempo,
        contador,
        reiniciar,
        rodada,
        finalJogo,
        tempoFinal,
        jogarNovamente,
        outroJogador
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
  margin-top: 10px;
  /* height: 100vh; */
}

.titulo h1{
  font-family: 'Assassin$ Regular';
  font-size: 60px;
  margin: 10px auto 30px;
}

.titulo img{
  width: 100px;
  margin-top: 10px;
}

.subtitulo{
  margin: 50px auto ;
}

.subtitulo p{
  margin: 5px auto;
  font-size: 20px;
  font-weight: 600;
}

.tabuleiro{
  display: grid;
  grid-template-columns: repeat(5, 100px);
  grid-template-rows: repeat(4, 150px);
  grid-column-gap: 25px;
  grid-row-gap: 25px;
  justify-content: center;
}

.status{
  margin: 25px auto;
  font-size: 20px;
  font-weight: 600;
}

.btn{
  background-color: #6a9eb8;
  border: 1px solid #6a9eb8;
  border-radius: 10px;
  color: #fff;
  padding: 0.75rem 0.5rem;
  margin: 15px auto 25px;;
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

.placar{
  display: grid;
  grid-template-columns: repeat(3, 200px);
  grid-template-rows: 100px;
  grid-column-gap: 25px;
  grid-row-gap: 25px;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}
.placar .nome{
  padding-top: 10px;
}
.placar .nome h2{
  font-family: 'Assassin$ Regular';
  font-size: 35px
}

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 350px;
  height: 420px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #172937;
  border-radius: 5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header{
  text-align: center;
  color: #fff
}

.modal-header h3 {
  font-family: 'Assassin$ Regular';
  font-size: 30px;
}

.modal-body {
  margin: 20px 0;
  text-align: center;
}

.modal-body input{
  background-color: #172937;
  border: none;
  border-bottom: 1px solid #fff;
  width: 250px;
  height: 40px;
  color: #fff;
  text-align: center;
}

.modal-body input:focus{
  outline: none;
}

.modal-default-button {
  float: right;
}

.modal-footer{
  margin-top: 90px;
}

.modal-footer button{
  width: 200px;
  font-family: 'Assassin$ Regular';
  font-size: 25px;
}
/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

#modal-placar .modal-container {
  width: 600px;
}

#modal-placar  .modal-body{
  margin: 106px auto;
}

#modal-placar  .modal-body p{
  color: white;
  font-size: 25px;
}

#modal-placar  .modal-body p span{
  color: #08adff;;
  font-weight: 900;
}

#modal-placar  .modal-body p:nth-child(1){
  text-transform: capitalize;
  font-size: 25px;
}

#modal-placar  .modal-body p:nth-child(1){
  padding: 0 80px;
}


#modal-placar  .modal-footer{
  display: flex;
  justify-content: space-around;
}


</style>
