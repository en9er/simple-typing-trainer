<template>
  <div id="app">
    <TypingTrainer />
  </div>
</template>

<script>
import TypingTrainer from "@/components/TypingTrainer";

export default {
  name: "App",
  components: {
    TypingTrainer
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  height: 100vh;
}
</style>
