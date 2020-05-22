<template>
  <div>
    <div class=logo>
      <img :src= "require(`@/data/img/petplaypalsnewlogo.png`)" class="title-image" />
    </div>
    <!-- <div>
      <img :src= "require(`@/data/img/petsgeekbanner.png`)" class = "header-image" />
    </div> -->
  </div>
</template>

<script>
export default {
  name: 'Header'
}
</script>

<style>
/* .header-image {
 
    max-width : 100%;
    height: auto;
} */

.title-image {
  width: 39%;
  max-width: 100%;
  height: auto;
}

.logo {
  background-color: gray;
  background-image: radial-gradient(circle,  white 40%,rgb(22, 22, 24));
}

</style>