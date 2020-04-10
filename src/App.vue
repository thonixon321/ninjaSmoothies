<template>
  <div id="app" class="appStyles">
   <Navbar />
   <router-view></router-view>
  </div>
</template>

<script>

import Navbar from './components/Navbar';

export default {
  name: 'app',

  data: function() {
    return {

    }
  },

  components: {
    Navbar
  }

}
</script>

<style>
  .navbar nav {
    padding: 0 20px;
  }
</style>
