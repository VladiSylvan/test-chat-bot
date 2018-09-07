<template>
  <div id="app">
    <div class="test"
         @keyup="showSelect($event)"
         ref="text"
         contenteditable="true">
    </div>
    <div ref="menu"
         @keyup="showSelect($event)"
         v-show="showMenu"
         class="item-list"
         :style="{'left': dropDownCoordinates.left + 'px', 'top': dropDownCoordinates.top + 'px'}"
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
      let newText = this.$refs.text.innerHTML
      let regex = /({{[a-zA-Z0-9]+}}|{{\S*\s+(\S+)}})/g
      let item = newText.match(regex);
      item.forEach((el) => {
        let result = this.data.filter(obj => {
        return obj.el == el.slice(2, -2)
        })
        let newItem = '&nbsp<span contenteditable="false" class="' + result[0].type + '">' + el + '</span>&nbsp;'
        let regex1 =  new RegExp('\([^>]' + el + ')|(^' + el + ')' );
        this.$refs.text.innerHTML = this.$refs.text.innerHTML.replace(regex1, newItem)
      })
    }
  },
  methods: {
    getKeyByValue(object, value) {
      return Object.keys(object).find(key => object[key] === value);
    },
    getCaretPosition(editableDiv) {
      var sel = 0,
          range;
      if (window.getSelection) {
        sel = window.getSelection();
        if (sel.rangeCount) {
          range = sel.getRangeAt(0);
        }
      } else if (document.selection && document.selection.createRange) {
        range = document.selection.createRange();
        if (range.parentElement() == editableDiv) {
          var tempEl = document.createElement("span");
          editableDiv.insertBefore(tempEl, editableDiv.firstChild);
          var tempRange = range.duplicate();
          tempRange.moveToElementText(tempEl);
          tempRange.setEndPoint("EndToEnd", range);
        }
      }
      this.dropDownCoordinates.left = this.$refs.text.offsetLeft + range.endOffset * 7
      this.dropDownCoordinates.top = this.$refs.text.offsetTop + (this.$refs.text.children.length > 0 ? this.$refs.text.children.length * 25 : 25)
    },
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
    showSelect(evt) {
      if (/{{\B/g.test(this.$refs.text.textContent) ) {
        this.showMenu = true
        if (evt.key === "ArrowUp") {
          this.placeCaretAtEnd(this.$refs.text)
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
        } else {
        this.showMenu = false
      }

      this.getCaretPosition(this.$refs.text)
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
