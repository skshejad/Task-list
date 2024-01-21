<template>
  <div id="app" class="container mx-auto p-4">
    <taskList  />
    
  </div>
</template>

<script>
import taskList from './components/taskList.vue';




export default {
  data() {
    return {
      tasks: [],
    };
  },
 
  components: {
   
    taskList,
  },
};
</script>

<!-- <style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style> -->
