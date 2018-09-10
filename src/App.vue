<template>
  <div id="app">
    <div class="test"
         @keyup="showSelect($event)"
         @keydown="keyPress($event)"
         ref="text"
         contenteditable="true">
    </div>
    <div ref="menu"
         tabindex="0"
         v-show="showMenu"
         @keydown="keyPress($event)"
         @keyup="keyUp($event)"
         class="item-list"
         :style="{'left': dropDownCoordinates.left + 'px', 'top': dropDownCoordinates.top + 20 + 'px'}"
         contenteditable="false">
      <ul>
        <li v-for="item in data"
            :key="item.index"
            :class="currentTarget == item.el ? 'lister' : item.type"
            @click="addText(item.type, item.el)">{{item.el}}
        </li>
      </ul>
    </div>
    <button class="submit"
            @click="submit()">submit</button>

  </div>
</template>

<script>

export default {
  name: 'app',
  data: () => ({
    text: '',
    info: {'system': ['email', 'first name'],  'list': ['gender2', 'age']},
    showMenu: false,
    dropDownCoordinates: {left: 0, top: 0},
    currentTargetId: 0,
    currentTarget: null,
    data: []
  }),
  created () {
    for (var el in this.info) {
      this.info[el].forEach(e => {
      this.data.push({type: el, el: e})
      })
    }
  },
  mounted () {
    if (window.localStorage.text) {
      this.$refs.text.innerText = window.localStorage.text

      let regex = /({{[a-zA-Z0-9]+}}|{{[a-zA-Z0-9]+\s+[a-zA-Z0-9]+}})/
      let textArray = []
      textArray = this.$refs.text.innerHTML.split(regex)
      textArray.forEach((el, index) => {
        this.data.filter(obj => {
          if(obj.el === el.slice(2, -2)) {
            let newItem = '<span contenteditable="false" class="' + obj.type + '">' + el + '</span>'
            textArray[index] = newItem
          }
        })
      })
      let textString = textArray.join('')
      this.$refs.text.innerHTML = textString
    }
  },
  methods: {
    placeCaretAtEnd(el) {
      el.focus();
      if (typeof window.getSelection != "undefined" && typeof document.createRange != "undefined") {
        var range = document.createRange();
        range.selectNodeContents(el);
        range.collapse(false); 
        var sel = window.getSelection();
        sel.removeAllRanges();
        sel.addRange(range);

      } else if (typeof document.body.createTextRange != "undefined") {
        var textRange = document.body.createTextRange();
        textRange.moveToElementText(el);
        textRange.collapse(false);
        textRange.select();
      }
    },
    keyPress(evt) {
      if (/{{\B/g.test(this.$refs.text.innerHTML) ) {
        if (evt.key === "ArrowUp") {
          this.currentTargetId -= 1
          if (this.currentTargetId < 0) {
            this.currentTargetId = this.data.length - 1
          }
          this.currentTarget = this.data[this.currentTargetId].el
          }
        if (evt.key === "ArrowDown") {
          this.currentTargetId += 1
          if (this.currentTargetId > this.data.length - 1) {
            this.currentTargetId = 0
          }
          this.currentTarget = this.data[this.currentTargetId].el
          }
      }
    },
    keyUp(evt) {
      if (/{{\B/g.test(this.$refs.text.innerHTML) ) {
        if (evt.key === "Enter") {
          this.addText(this.data[this.currentTargetId].type, this.data[this.currentTargetId].el)
        }  
      }
    },
    getSelectionTopLeft() {
      var x = 0, y = 0;
      if (window.getSelection && document.createRange
      && typeof document.createRange().getBoundingClientRect != "undefined") {
          var sel = window.getSelection();
          if (sel.rangeCount > 0) {
              var rect = sel.getRangeAt(0).getBoundingClientRect();
              x = rect.left;
              y = rect.top;
          }
      } else if (document.selection && document.selection.type != "Control") {
          var textRange = document.selection.createRange();
          x = textRange.boundingLeft;
          y = textRange.boundingTop;
      }
      return { x: x, y: y };
    },
    showSelect() {
      if (this.getSelectionTopLeft() !== undefined) {
        this.dropDownCoordinates.left = this.getSelectionTopLeft().x
        this.dropDownCoordinates.top = this.getSelectionTopLeft().y
       }
      if (/{{\B/g.test(this.$refs.text.innerHTML) ) {
        this.$refs.menu.focus();
        this.showMenu = true    
        } else {
        this.showMenu = false
      }
    },
    submit() {
      window.localStorage.setItem('text', this.$refs.text.innerText)
    },
    addText(item, el) {
      this.showMenu = false
      const newItem = '<span contenteditable="false" class="' + item + '">' +  '{{' + el + '}}' + '</span>&nbsp;'
      this.$refs.text.innerHTML = this.$refs.text.innerHTML.replace(/{{\B/g, newItem)
      this.placeCaretAtEnd(this.$refs.text)
    },
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
  display: flex;
  flex-direction: column;
  align-items: center;
}
.test {
  border-radius: 5px;
  padding: 5px;
  margin-bottom: 20px;
  width: 200px;
  min-height: 100px;
  text-align: left;
  -webkit-box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
  -moz-box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
  box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
}
.system {
  background-color: #66bb6a;
  width: fit-content;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
}
.list {
  background-color: #42a5f5;
  width: fit-content;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
}
.lister {
  background-color: #f2f5f5;
  width: fit-content;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
}
.item-list {
  background-color: white;
  position: absolute;
  padding: 5px 5px 1px 5px;
  width: fit-content;
  border-radius: 5px;
  margin: auto;
  -webkit-box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
  -moz-box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
  box-shadow: -1px 3px 24px 3px rgba(0, 0, 0, 0.14);
}
ul {
  list-style: none;
  margin: 0;
  padding: 0;
  text-align: left;
}
li {
  cursor: pointer;
  margin-bottom: 5px;
  padding: 3px;
  color: white;
}
li:before {
  content: "{{";
}
li:after {
  content: "}}";
}
.submit {
  width: 100px;
  height: 30px;
  background: none;
  color: inherit;
  border: none;
  background: #66bb6a;
  padding: 0;
  font: inherit;
  cursor: pointer;
  outline: inherit;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
  text-transform: uppercase;
}
</style>
