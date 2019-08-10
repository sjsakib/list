<template>
  <div id="app">
    <button @click="addTable">+</button>
    <div v-for="id in tables" :key="id">
      <Table v-bind:id="id"/>
    </div>
  </div>
</template>

<script>
import Table from './components/Table.vue'

export default {
  name: 'app',
  data: () => ({
    tables: []
  }),
  methods: {
    addTable: function() {
      this.tables.unshift(+new Date())
    }
  },
  components: {
    Table
  }
}
</script>

<style scoped>
#app {
  font-family: Roboto, sans-serif;
  color: rgb(51, 51, 51);

  display: flex;
  flex-flow: column;
  align-items: center;
}

button {
  background-color: white;
  font-size: 1.5rem;
  width: 2rem;
  height: 2rem;
  border-radius: 50%;
  border: none;
  box-shadow: 1px 1px 2px #bbb;
  cursor: pointer;
  outline: 0;
}

button:active {
  box-shadow: 1px 1px #bbb;
}

</style>
