<template>
  <div id="app">
    <link href="https://fonts.googleapis.com/css?family=Raleway" rel="stylesheet">
    <div class="color-mode-switcher-container">
      <button class="button-primary color-mode-switcher" @click="changeColorMode()">Change colors</button>
    </div>
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data() {
    return {
      isDarkModeOn: true
    }
  },
  methods: {
    changeColorMode() {
      console.log('changeColorMode method');
      if (this.isDarkModeOn) {
        this.turnLightModeOn()
      } else {
        this.turnDarkModeOn()
      }
    },
    turnDarkModeOn() {
      const root = document.documentElement;
      root.style.setProperty('--primary-color', 'rgb(43, 255, 0)');
      root.style.setProperty('--main-grey', 'rgb(50, 50, 50)');
      root.style.setProperty('--box-shadow-color', 'rgba(255, 255, 255, 0.3)');
      root.style.setProperty('--main-text-color', 'rgb(220, 220, 220)');
      root.style.setProperty('--main-background-color', 'rgb(20, 20, 20)');
      this.isDarkModeOn = true;
    },
    turnLightModeOn() {
      const root = document.documentElement;
      root.style.setProperty('--primary-color', 'rgb(97, 0, 189)');
      root.style.setProperty('--main-grey', 'rgb(185, 185, 185)');
      root.style.setProperty('--box-shadow-color', 'rgba(0,0,0,0.6)');
      root.style.setProperty('--main-text-color', 'rgb(40, 40, 40)');
      root.style.setProperty('--main-background-color', 'rgb(250, 250, 250)');
      this.isDarkModeOn = false;
    }
  }
}
</script>

<style>
:root {
  --animation-duration: 0.4s;
}

/* Light mode */
/* :root {
  --primary-color: rgb(97, 0, 189);
  --main-grey: rgb(185, 185, 185);
  --box-shadow-color: rgba(0,0,0,0.6);
  --main-text-color: rgb(40, 40, 40);
  --main-background-color: rgb(250, 250, 250);
} */

/* Dark mode */
:root {
  --primary-color:  rgb(43, 255, 0);
  --main-grey: rgb(50, 50, 50);
  --box-shadow-color: rgba(255, 255, 255, 0.3);
  --main-text-color: rgb(220, 220, 220);
  --main-background-color: rgb(20, 20, 20);
}

html {
  width: 100%;
  height: 100%;
  margin: 0;
}

body {
  width: 100%;
  min-height: 100%;
  margin: 0;
  background-color: var(--main-background-color);
  color: var(--main-text-color);
}

#app {
  font-family: 'Raleway', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: var(--main-text-color);
  background-color: var(--main-background-color);
  position: relative;
}

a {
  color: var(--primary-color);
}

a:hover {
  font-weight: bold;
}

button {
  font-family: 'Raleway', sans-serif;
  font-weight: bold;
  padding: 0.5em 1.0em;
  text-align: center;
  display: inline-block;
  font-size: 1em;
  border: 1px solid;
  transition-duration: var(--animation-duration);
}

button:focus {
  outline: 0;
}

.button-primary {
  background-color: transparent;
  color: var(--primary-color);
  border-color: var(--primary-color);
}

.button-primary:hover {
  background-color: var(--primary-color);
  color: var(--main-background-color);
  border-color: transparent;
  cursor: pointer;
}

.slider {
  -webkit-appearance: none;
  height: 0.4em;
  background: var(--main-grey);
  outline: none;
  opacity: 0.7;
  -webkit-transition: var(--animation-duration);
  transition: opacity var(--animation-duration);
}

.active-slider:hover {
  opacity: 1.0;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 1.2em;
  height: 1.2em;
  border-radius: 50%;
}

.active-slider::-webkit-slider-thumb {
  background: var(--primary-color);
  cursor: pointer;
  animation-name: slider-from-disabled-to-active;
  animation-duration: var(--animation-duration);
}

.disabled-slider::-webkit-slider-thumb {
  background: var(--main-grey);
  cursor: not-allowed;
  animation-name: slider-from-active-to-disabled;
  animation-duration: var(--animation-duration);
}

@keyframes slider-from-active-to-disabled {
  from {background-color: var(--primary-color);}
  to {background-color: var(--main-grey);}
}

@keyframes slider-from-disabled-to-active {
  from {background-color: var(--main-grey);}
  to {background-color: var(--primary-color);}
}

.color-mode-switcher-container {
  position: absolute;
  top: 1rem;
  right: 1rem;    
}

.color-mode-switcher {
  font-size: 0.8rem;
}
</style>
