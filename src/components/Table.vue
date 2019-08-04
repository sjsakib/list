<template>
  <div>
    <form
      v-if="editing[0] === -1 && editing[1] === -1"
      @submit.prevent="editing = [null, null]"
    >
      <input
        ref="titleInput"
        autofocus
        @blur="editing = [null, null]"
        v-model="title"
      />
    </form>
    <h4 v-else @click="editTitle">
      {{
        title ||
          new Date(id).toLocaleString('en-US', {
            month: 'short',
            day: 'numeric',
            hour: '2-digit',
            minute: '2-digit'
          })
      }}
    </h4>
    <table>
      <tr>
        <th
          v-for="(key, i) in columns"
          :key="i + key"
          @click="setEditing(-1, i)"
        >
          <form
            v-if="editing[0] === -1 && editing[1] === i"
            @submit.prevent="save($event.target.cell.value, -1, i)"
          >
            <input autofocus ref="cellInput" :value="key" name="cell" />
          </form>
          <span v-else>{{ key }}</span>
        </th>
        <td><button @click="addColumn">+</button></td>
      </tr>
      <tr v-for="(row, r) in rows" :key="r + row.join()">
        <td v-for="(val, c) in row" :key="c + val">
          <form
            v-if="editing[0] === r && editing[1] === c"
            @submit.prevent="save($event.target.cell.value, r, c)"
          >
            <input autofocus ref="cellInput" :value="val" name="cell" />
          </form>
          <span @click="setEditing(r, c)" v-else>{{ val || '--' }}</span>
        </td>
      </tr>
      <tr>
        <td>
          <form @submit="newRow">
            + <input placeholder="New item" name="cell" />
          </form>
        </td>
      </tr>
      <tr>
        <th v-for="(sum, i) in sums" :key="`${i}${sum}`">
          {{ sum }}
        </th>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: 'data-table',

  data: () => ({
    title: '',
    columns: ['C1'],
    rows: [],
    editing: [null, null]
  }),

  methods: {
    newRow: function(e) {
      e.preventDefault();
      const newRow = new Array(this.columns.length).fill('');
      newRow[0] = Number(e.target.cell.value) || e.target.cell.value;
      this.rows = [...this.rows, newRow];
      e.target.cell.value = '';
      this.setEditing(this.rows.length - 1, 1);
    },

    save: function(value, r, c) {
      const val = Number(value) || value;
      if (r === -1) this.$set(this.columns, c, val);
      else this.$set(this.rows[r], c, val);

      if (r === this.rows.length - 1) this.setEditing(r, c + 1);
      else this.setEditing(r + 1, c);
    },

    setEditing: function(r, c) {
      if (r >= this.rows.length || c >= this.columns.length) {
        this.editing = [null, null];
        return;
      }
      this.editing = [r, c];
      this.$nextTick(function() {
        this.$refs.cellInput[0].focus();
      });
    },

    addColumn: function() {
      this.columns = [...this.columns, 'C' + (this.columns.length + 1)];
      this.rows = this.rows.map(r => [...r, '']);
      this.setEditing(-1, this.columns.length - 1);
    },

    editTitle: function() {
      this.editing = [-1, -1];
      this.$nextTick(function() {
        this.$refs.titleInput.focus();
      });
    }
  },

  computed: {
    sums: function() {
      return this.columns.map((key, i) => {
        const s = this.rows.reduce((p, c) => p + Number(c[i]), 0);
        return isNaN(s) ? '' : s;
      });
    }
  },
  props: ['id']
};
</script>

<style scoped></style>
