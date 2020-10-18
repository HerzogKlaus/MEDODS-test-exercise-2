<template>
  <div id="app">
    <Game />
  </div>
</template>

<script>
import './scss/style.scss'
import Game from "./components/Game";

export default {
  name: "App",
  components: {
    Game,
  },
};
</script>