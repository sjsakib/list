<template>
  <div>
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
            <OptionMenu
              :actions="[
                ['Edit', () => setEditing(-1, i)],
                ['Delete column', () => removeColumn(i)]
              ]"
              v-else
            >
              {{ key }}
            </OptionMenu>
          </th>
          <td class="action-column"><button @click="addColumn">+</button></td>
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
          <td v-if="editing[0] === r" class="action-column">
            <button @click="removeRow(r)">-</button>
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
    <confirm ref="confirm" />
  </div>
</template>

<script>
import Confirm from './Confirm';
import OptionMenu from './OptionMenu';

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

    removeRow: async function(r) {
      if (await this.$refs.confirm.confirm('Sure to delete row?')) {
        this.rows.splice(r, 1);
      }
    },

    removeColumn: async function(c) {
      if (this.columns.length === 1) return;
      if (await this.$refs.confirm.confirm('Sure to delete column?')) {
        this.columns.splice(c, 1);
        this.rows.forEach(row => row.splice(c, 1));
      }
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
      this.$refs['columnCell-' + i][0].style.width = `${Math.max(
        10,
        Math.min(maxLen, 50)
      ) * 12}px`;
    });
  },

  props: ['id'],

  components: {
    Confirm,
    OptionMenu
  }
};
</script>

<style src="./Table.css" scoped />
