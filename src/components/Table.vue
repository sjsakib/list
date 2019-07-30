<template>
  <table>
    <th>
      <td v-for="key in Object.keys(rows[0])">
        {{key || new Date(id).toLocaleTimeString()}}
      </td>
    </th>
    <tr v-for="row in rows.slice(1)">
      <td v-for="key in Object.keys(row)">{{row[key]}}</td>
    </tr>
    <tr>
      <td v-for="key in Object.keys(rows[0])">
        <form @submit="saveCell($event, key)"><input name="value"/></form>
      </td>
    </tr>
    <th>
      <td v-for="key in Object.keys(rows[0])">
        {{rows.reduce((p, c) => p+Number(c[key]), 0) || '-'}}
      </td>
    </th>
  </table>
</template>

<script>
export default {
  name: 'data-table',
  data: () => ({rows: [{'': ''}]}),
  methods: {
    saveCell: function(e, key) {
      e.preventDefault();
      this.rows.push({[key]: e.target.value.value});
      e.target.value.value = '';
      e.target
    }
  },
  props: ['id'],
}
</script>

<style scoped>

</style>