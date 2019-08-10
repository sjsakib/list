<template>
  <table>
    <caption>
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
      <span class="table-title" v-else @click="editTitle">
        {{
          title ||
            new Date(id).toLocaleString('en-US', {
              month: 'short',
              day: 'numeric',
              hour: '2-digit',
              minute: '2-digit'
            })
        }}
      </span>
    </caption>
    <thead>
      <tr>
        <th
          v-for="(key, i) in columns"
          :key="i + '-' + key"
          @click="setEditing(-1, i)"
          :ref="'columnCell-' + i"
        >
          <form
            v-if="editing[0] === -1 && editing[1] === i"
            @submit.prevent="save($event.target.cell.value, -1, i)"
          >
            <input
              autofocus
              ref="cellInput"
              :value="key"
              name="cell"
              placeholder="--"
            />
          </form>
          <span v-else>{{ key }}</span>
        </th>
        <td class="add-column"><button @click="addColumn">+</button></td>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, r) in rows" :key="r + row.join()">
        <td v-for="(val, c) in row" :key="c + val">
          <form
            v-if="editing[0] === r && editing[1] === c"
            @submit.prevent="save($event.target.cell.value, r, c)"
          >
            <input
              autofocus
              ref="cellInput"
              :value="val"
              name="cell"
              placeholder="--"
            />
          </form>
          <span @click="setEditing(r, c)" v-else>{{ val || '--' }}</span>
        </td>
      </tr>
      <tr>
        <td :colspan="columns.length">
          <form @submit="newRow">
            <input placeholder="+ New item" name="cell" />
          </form>
        </td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <th v-for="(sum, i) in sums" :key="`${i}${sum}`">
          {{ sum }}
        </th>
      </tr>
    </tfoot>
  </table>
</template>

<script>
export default {
  name: 'data-table',

  data: () => ({
    title: '',
    columns: ['Column 1'],
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

  updated: function() {
    this.columns.forEach((c, i) => {
      let maxLen = this.rows.reduce((p, c) => Math.max(p, c[i].length), 0);
      maxLen = Math.max(maxLen, this.columns[i].length);
      console.log(i, maxLen);
      this.$refs['columnCell-' + i][0].style.width = `${Math.max(
        10,
        Math.min(maxLen, 50)
      ) * 12}px`;
    });
    console.log(this);
  },
  props: ['id']
};
</script>

<style scoped>
table {
  padding: 2rem;
  margin-top: 1rem;
  border-radius: 0.3rem;
  box-shadow: 1px 1px 2px #bbb;
}

table {
  border-collapse: collapse;
}

table caption {
  font-size: 1.3rem;
  font-weight: bold;
  margin: 1rem 0;
}

table thead tr:first-child th {
  color: #7d7d7d;
  position: relative;
  border-bottom: 1px solid #0000001a;
}

table thead tr:first-child td.add-column {
  background-color: white;
  width: 10px;
}

table thead tr:first-child td.add-column button {
  background-color: white;
  border: 1px solid #0000001a;
  border-radius: 30%;
  /* box-shadow: 1px 1px 2px #bbb;*/
  outline: 0;
}

table thead tr:first-child th:not(:first-child)::before {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  height: 1rem;
  width: 2px;
  background-color: #0000001a;
}

table tbody tr:nth-child(even):not(:last-child) {
  background-color: #fafafa;
}

table td,
th {
  word-wrap: wrap;
  text-align: left;
  padding: 0.4rem 0.8rem;
}

form {
  width: 100%;
}

input {
  border: none;
  width: 100%;
  background-color: inherit;
  font-size: inherit;
  font-weight: inherit;
  color: inherit;
  text-align: inherit;
  outline: 0;
}
</style>
