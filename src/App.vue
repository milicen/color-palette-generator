<template>
  <main>
    <h1>Color Palette Generator</h1>
    <ul class="card-container">
      <li v-for="(color, index) in colors" :key="index">
        <ColorCard :color="color" :ref="el => {if (el) cards.push(el)}"/>
      </li>  
    </ul>  
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
import {onBeforeUpdate, ref} from 'vue'
import {toClipboard} from '@soerenmartius/vue3-clipboard'

export default {
  name: 'App',
  components: {
    ColorCard
  },
  setup() {
    let cards = ref([])
    let colors = ref([[210,210,210],[210,210,210],[210,210,210],[210,210,210],[210,210,210]])
    
    function copyAllColors() {
      let arr = []
      cards.value.forEach(card => {
        arr.push(card.hexValue)
      });
      toClipboard(arr.join(' '))
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

    function fetchColor() {
      console.log('fetch color')
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
    }        

    onBeforeUpdate(() => {
      cards.value.splice(0, cards.value.length)
    })

    return {cards, colors, fetchColor, copyAllColors}
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
    margin-bottom: 1em;
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

  .buttons {
    width: fit-content;
    margin: 0 auto;
    display: flex;
    gap: 2em;
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
</style>
