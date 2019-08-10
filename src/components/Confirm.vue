<template>
  <div v-if="show" class="container">
    <div class="inner-container">
      <div class="message">{{ message }}</div>
      <div class="buttons">
        <button @click="done(false)">Cancel</button>
        <button @click="done(true)">Confirm</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return { message: '', show: false };
  },
  methods: {
    confirm: function(message) {
      this.message = message;
      this.show = true;
      return new Promise(resolve => {
        this.resolve = resolve;
      });
    },
    done: function(res) {
      this.show = false;
      this.resolve(res);
    }
  }
};
</script>

<style scoped>
.container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;

  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #000000aa;
}

.inner-container {
  padding: 1.5rem;
  background-color: white;
  border-radius: .3rem;
}

.message {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 1.8rem;
}

.buttons {
  display: flex;
  justify-content: flex-end;
}

.buttons button {
  float: left;
  margin-left: 1.5rem;
  border: none;
  background-color: white;
  font-weight: bold;
  outline: 0;
  color: #444;
  cursor: pointer;
}


</style>
