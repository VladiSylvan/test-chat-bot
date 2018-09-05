<template>
  <div id="app">
    <div class="test"
         @keyup.capture="showSelect($event)"
         ref="text"
         contenteditable="true">
    </div>
    <button @click="submit()">submit</button>
    <ul v-if="showMenu"
        v-for="item in data"
        :key="item.index"
        :class="data['system'] === item ? 'system' : 'list'">
      <li v-for="el in item"
          :key="el.id"
          @click="addText(item, el)">{{el}}</li>
    </ul>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: () => ({
    text: `asdasdasd asdas das`,
    data: {'system': ['email', 'first name'],  'list': ['gender2', 'age']},
    showMenu: false
  }),
  methods: {
    showSelect() {
      if (/{{$/g.test(this.$refs.text.textContent)) {
        this.showMenu = true
      } else {
        this.showMenu = false
      }
    },
    submit() {
      this.axios.post('', this.$refs.text.innerText)
    },
    addText(item, el) {
      this.showMenu = false
      const newItem = '<span style="' + (this.data['system'] === item ? 'background-color: lightgreen;' : 'background-color: lightblue;') + '">' +  '{{' + el + '}}' + '</span>&nbsp;'
      if (this.$refs.text.children[this.$refs.text.children.length - 1] !== undefined) {
       this.$refs.text.children[this.$refs.text.children.length - 1].innerHTML  = this.$refs.text.children[this.$refs.text.children.length - 1].innerHTML.slice(0, -2) + newItem
      } else {
        this.$refs.text.innerHTML = this.$refs.text.innerHTML.slice(0, -2) + newItem;
      }    
      this.$refs.text.children[this.$refs.text.children.length - 1].focus()
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.test {
  margin: auto;
  width: 200px;
  border: 1px solid black;
  text-align: left;
}
.text-area {
  height: fit-content;
  min-height: 100px;
}
.system {
  background-color: lightgreen;
}
.list {
  background-color: lightblue;
}
</style>
