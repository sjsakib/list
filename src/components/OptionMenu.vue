<template>
  <div ref="content" class="content" @click="showMenu = !showMenu">
    <slot />
    <div v-if="showMenu" class="menu">
      <span
        class="menu-item"
        :key="action[0]"
        v-for="action in actions"
        @click="action[1]()"
      >
        {{ action[0] }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return { showMenu: false };
  },
  methods: {
    toggle: function(e) {
      if (this.$refs.content.contains(e.target)) return;
      this.showMenu = !this.showMenu;
    }
  },
  watch: {
    showMenu: function(show) {
      if (show) {
        window.addEventListener('click', this.toggle, true);
      } else {
        window.removeEventListener('click', this.toggle, true);
      }
    }
  },

  beforeDestroy: function() {
    window.removeEventListener('click', this.toggle, true);
  },
  props: ['actions']
};
</script>

<style>
.content {
  position: relative;
  cursor: pointer;
  width: 100%;
}
.menu {
  background-color: white;
  width: 100%;
  position: absolute;
  border: 1px solid #0000001a;
  border-radius: 0.3rem;
  font-size: 0.8rem;
  font-weight: normal;
}

.menu-item {
  padding: 0.5rem;
  display: block;
  border-radius: 0.3rem;
}

.menu-item:hover {
  background-color: #f7f7f7;
}
</style>
