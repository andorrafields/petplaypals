<template>
  <div id="app">
    <div>
      <div>
      <Header />
      </div>
      <div>
      <navigation />
      </div>
    </div>
    <router-view/>
  </div>
</template>

<script>
import Header from '@/component/Header'

export default {
  components: {
    Header,
  },
}

</script>

<style>
  body {
    font-family: sans-serif;
    margin: 0vw;
  }



  
</style>