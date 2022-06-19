<template>
  <main>
    <h1>Color Palette Generator</h1>
    <Alert :isShow="canShowMessage">
      {{message}} copied!
    </Alert>
    <ul class="card-container">
      <li v-for="(color, index) in colors" :key="index">
        <ColorCard 
          :color="color" 
          @click="showMessage(cards[index].hexValue)"
          :ref="el => {if (el) cards.push(el)}"/>
      </li>  
    </ul>  
    <small class="info">Click on any color to copy its hex value</small>
    <div class="buttons">
      <div class="btn-wrapper">
        <button @click="fetchColor">
          Generate palette!
        </button>
        <small>or press Spacebar</small>
      </div>
      
      <div class="btn-wrapper">
        <button @click="copyAllColors">
          Copy colors!
        </button>
        <small>or press 'c'</small>
      </div>

    </div>
    
  </main>
</template>

<script>
import ColorCard from './components/ColorCard.vue'
import Alert from './components/Alert.vue'
import {onBeforeUpdate, ref} from 'vue'
import {toClipboard} from '@soerenmartius/vue3-clipboard'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    ColorCard,
    Alert
  },
  setup() {
    let cards = ref([])
    let colors = ref([[210,210,210],[210,210,210],[210,210,210],[210,210,210],[210,210,210]])
    let canShowMessage = ref(false)
    let message = ref('')

    function showMessageCountdown() {
      canShowMessage.value = true
      setTimeout(() => {
        canShowMessage.value = false
      }, 2000)
    }

    function setMessage(value) {
      message.value = value
    }

    function showMessage(msg) {
      setMessage(msg)
      showMessageCountdown()
    }

    function copyAllColors() {
      let arr = []
      cards.value.forEach(card => {
        arr.push(card.hexValue)
      });
      toClipboard(arr.join(' '))
      showMessage('Colors')
    }

    let copyAllFired = false
    let generateNewFired = false
    window.addEventListener('keydown', (event) => {
      if(event.key === "c" || event.key === "C") {
        if(copyAllFired) {
          return
        }
        copyAllFired = true
        copyAllColors()
      }
      else if(event.key === " " || event.key === " ") {
        if(generateNewFired) {
          return
        }
        generateNewFired = true
        fetchColor()
      }
    })

    window.addEventListener('keyup', (event) => {
      if(event.key === "c" || event.key === "C") {
        copyAllFired = false
      }
      else if(event.key === " " || event.key === " ") {
        generateNewFired = false
      }
    })

    async function fetchColor() {
      console.log('fetch color')
      // if(window.location.origin.match(/localhost/)) {
        var url = "http://colormind.io/api/";
        var data = {
          model : "default"
        }
        var http = new XMLHttpRequest();
        http.onreadystatechange = function() {
          if(http.readyState == 4 && http.status == 200) {
            colors.value = [...JSON.parse(http.responseText).result];
          }
          else {
            console.log('error')
          }
        }

        http.open("POST", url, true);
        http.send(JSON.stringify(data));
      // } else {
      //   var url = "https://colormind.io/api/";
      //   var data = {
      //     model : "default"
      //   }
      //   var request = await axios.post(url, data)
      //   console.log(request)
      //   colors.value = [...request.result]
      // }
      
    }        

    onBeforeUpdate(() => {
      cards.value.splice(0, cards.value.length)
    })

    return {
      cards, 
      colors, 
      canShowMessage, 
      message, 
      showMessage, 
      fetchColor, 
      copyAllColors
    }
  },
  mounted() {
    this.fetchColor()
  }
}
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  font-size: 24px;
  height: 100vh;
  display: grid;
  place-items: center;
  background-color: antiquewhite;
}

main {
  width: fit-content; 

  h1 {
    margin-bottom: .5rem;
    color: rgb(129, 48, 24);
  }

  .card-container {
    list-style: none;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 1.5rem;
    margin-bottom: 2em;
  }

  .info {
    display: block;
    width: fit-content;
    margin: 0 auto 1em;
    color: rgb(129, 48, 24);
  }

  .buttons {
    width: fit-content;
    margin: 0 auto;
    display: flex;
    gap: .5em;
  }

  .btn-wrapper:nth-of-type(2) {
    button {
      border: 3px solid orange;
      background-color: transparent;
      color: orange;
    }
  }

  .btn-wrapper {
    button {
      position: relative;
      top: 0;
      background-color: orange;
      border: none;
      border-radius: 2em;
      font-size: 1.5rem;
      font-weight: bold;
      color: white;
      padding:  .5em 2em;
      margin-bottom: .5em;
      box-shadow: 0 .2rem 10px 1px rgba(0, 0, 0, 0.096);
      transition: all .3s;
    
      &:hover {
        top: -.2em;
        box-shadow: 0 .35rem 10px 1px rgba(0, 0, 0, 0.233);

      }

      
    }

    small {
      font-size: 1rem;
      font-weight: 100;
      font-style: italic;
      color: rgb(129, 48, 24);
      opacity: .6;
      display: block;
    }
  }
  
  
}

@media screen and (max-width: 1220px) {
  html {
    font-size: 14px;
  }
}

@media screen and (max-width: 700px) {
  html {
    font-size: 12px;
  }

  #app {
    height: 100vh;
  }

  main {
    padding: 3em 0;
  }
}

@media screen and (max-width: 550px) {
  #app {
    height: fit-content;
  }
}


</style>
