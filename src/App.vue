<template>
  <div id="app">
    <div class="test"
         @keyup="showSelect($event)"
         ref="text"
         contenteditable="true">
    </div>
    <div v-if="showMenu"
         class="item-list"
         :style="{'left': dropDownCoordinates.left + 'px', 'top': dropDownCoordinates.top + 'px'}"
         contenteditable="false">
      <ul v-for="item in info"
          :key="item.index">
        <li v-for="el in item"
            :key="el.id"
            :class="currentTarget == el ? 'lister' : (info['system'] === item ? 'system' : 'list')"
            @click="addText(item, el)">{{el}}
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
    text: `asdasdasd asdas das`,
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
  methods: {
    getCaretPosition(editableDiv) {
      var caretPos = 0,
          sel, range;
      if (window.getSelection) {
        sel = window.getSelection();
        if (sel.rangeCount) {
          range = sel.getRangeAt(0);
          if (range.commonAncestorContainer.parentNode == editableDiv) {
            caretPos = range.endOffset;
          }
        }
      } else if (document.selection && document.selection.createRange) {
        range = document.selection.createRange();
        if (range.parentElement() == editableDiv) {
          var tempEl = document.createElement("span");
          editableDiv.insertBefore(tempEl, editableDiv.firstChild);
          var tempRange = range.duplicate();
          tempRange.moveToElementText(tempEl);
          tempRange.setEndPoint("EndToEnd", range);
          caretPos = tempRange.text.length;
        }
      }
      this.dropDownCoordinates.left = this.$refs.text.offsetLeft + range.endOffset * 7
      this.dropDownCoordinates.top = this.$refs.text.offsetTop + (this.$refs.text.children.length > 0 ? this.$refs.text.children.length * 25 : 25)
    },
    showSelect(evt) {
      if (/{{\B/g.test(this.$refs.text.textContent) ) {
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
      if (evt.key === "Enter") {
        this.addText(this.data[this.currentTargetId].type, this.data[this.currentTargetId].el)
      }
        this.showMenu = true
      } else {
        this.showMenu = false
      }

      this.getCaretPosition(this.$refs.text)
    },
    submit() {
      this.axios.post('', this.$refs.text.innerText)
    },
    addText(item, el) {
      this.showMenu = false
      const newItem = '<span contenteditable="false" class="' + (this.info['system'] === item ? 'system' : 'list') + '">' +  '{{' + el + '}}' + '</span>&nbsp;'
      this.$refs.text.innerHTML = this.$refs.text.innerHTML.replace(/{{\B/g, newItem)
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
  position: relative;
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
  text-transform: capitalize;
}
.list {
  background-color: #42a5f5;
  width: fit-content;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
  text-transform: capitalize;
}
.lister {
  background-color: #f2f5f5;
  width: fit-content;
  border-radius: 5px;
  color: white;
  font-weight: 700;
  font-size: 13px;
  text-transform: capitalize;
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
  text-transform: capitalize;
}
</style>
