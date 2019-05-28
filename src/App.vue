<template>
  <div id="app">
    <div>
      <button class="button" @click="sendData">{{ buttonText }}</button>
      <div class="info-wrapper">
        <div v-if="!connected">Подключение к серверу...</div>
      </div>
    </div>
  </div>
</template>

<script>
import { setTimeout } from 'timers'

const wsUrl = "ws://pm.tada.team/ws"
const states = ['Медвед', 'Превед', '']
let state = 2
let ws = null

export default {
  name: 'app',
  data: function () {
    return {
      connected: false,
      buttonText: '',
    }
  },
  created: function () {
    ws = new WebSocket(wsUrl)
    this.addHandlers()
  },
  methods: {
    addHandlers() {
      ws.onopen = () => {
        this.connected = true
      };
      ws.onclose = () => {
        this.connected = false
        console.log('Попытка реконекта')
        setTimeout(() => {
          ws = new WebSocket(wsUrl)
          this.addHandlers()
        }, 1000)
      };
      ws.onmessage = (event) => {
        state = JSON.parse(event.data).state
        this.buttonText = states[state]
      };
      ws.onerror = (error) => {
        console.log("Ошибка " + error.message);
      };
    },
    sendData() {
      if (this.connected) {
        ws.send(JSON.stringify({ state: state ? 0 : 1 }))
      }
    }
  }
}
</script>

<style>
html, body {
    height: 100%;
}
#app {
  display: flex;
  height: 100%;
  width: 100%;
  justify-content: center;
  align-items: center;
}
.button {
  width: 200px;
  height: 60px;
  font-size: 30px;
  background: #00a5ff;
  border: none;
  color: white;
}
.button:active {
  background: #008fdd;
}
.info-wrapper {
  margin-top: 10px;
  height: 50px;
}
</style>
