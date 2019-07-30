<template>
  <div id="app">
    <button @click="addTable">+</button>
    <div v-for="id in tables">
      <Table v-bind:id="id" />
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
      this.tables.push(+new Date())
    }
  },
  components: {
    Table
  }
}
</script>

<style>
</style>
