<template>
  <div id="app">
    <input type="file" id="file" style="display: none;" @change="openFile">
    <div id="book" >
      <div class="page" ref="page">
        <div class="content" v-html="currentContent" v-if="currentContent"></div>
        <label v-else class="opener" for="file"  :class="{faded: isFileIn}">Drop file here<br>打开</label>
      </div>
    </div>
    <div class="menu">
      <div @click="prev">上一章</div>
      <div>第{{current}}章/共{{total}}章</div>
      <div @click="next">下一章</div>
    </div>
  </div>
</template>

<script>
import MobiFile from './utils/mobi'
// const {ipcRenderer} = require('electron')

export default {
  name: 'app',
  data: function () {
    return {
      myFiles: [],
      pages: [],
      current: 1,
      isFileIn: false
    }
  },
  computed: {
    total () {
      return this.pages.length
    },
    currentContent () {
      return this.pages[this.current - 1] && this.pages[this.current - 1].innerHTML
    }
  },
  watch: {
    current (now) {
      this.$refs.page.scrollTop = 0
    }
  },
  mounted () {
    const book = document.getElementById('book')
    console.log(book)
    book.ondragover = (e) => {
      event.preventDefault()
      event.stopPropagation()
      this.isFileIn = true
    }
    book.ondragleave = (e) => {
      event.preventDefault()
      event.stopPropagation()
      this.isFileIn = false
    }
    book.ondrop = (event) => {
      event.preventDefault()
      event.stopPropagation()
      console.log(event)
      var files = this.files || event.dataTransfer.files
      this.renderBook(files)
      // ipcRenderer.send('ondragstart', '/path/to/item')
    }
  },
  methods: {
    prev () {
      if (this.current > 1) {
        this.current -= 1
      }
    },
    openFile (e) {
      console.log(e)
      this.renderBook(e.target.files)
    },
    renderBook (files) {
      var reader = new FileReader()
      reader.onload = (event) => {
        var file_content = event.target.result
        let mobiFile = new MobiFile(file_content)
        mobiFile.render()
        console.log(mobiFile.pages)
        this.pages = mobiFile.pages
      }
      reader.readAsArrayBuffer(files[0])
    },
    next () {
      if (this.current < this.total) {
        this.current += 1
      }
    }
  }
}
</script>

<style>
body{
  padding: 0;
  margin: 0;
  height: 100vh;
}
#app{
  background-color: #e5e4db;
  height: 100%;
}
#app:after{
  content: '';
  clear: both;
  display: block;
}
.menu{
  position: absolute;
  top: 90px;
  cursor: pointer;
}
.page {
  overflow: scroll;
  position: relative;
  float: left;
  max-height: 48em;
  height: 90%;
  line-height: 1.6em;
  width: 34em;
  padding: 5em 4.6875em 2.5em;
  margin: 0;
  background: #f6f4ec;
  cursor: default;
}
body{
  font: 14px 'Helvetica Neue',Helvetica,'Lucida Grande','Luxi Sans',Arial,'PingFang SC','Hiragino Sans GB',STHeiti,'Microsoft YaHei','Wenquanyi Micro Hei','WenQuanYi Micro Hei Mono','WenQuanYi Zen Hei','WenQuanYi Zen Hei Mono',LiGothicMed;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
}
#book{
  display: flex;
  justify-content: center;
  height: 100%;
  align-items: center;
}
/* opener */
.opener{
  font-size: 40px;
  position: absolute;
  line-height: 1.5em;

  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.faded{
  opacity: 0.8;
  background-color: rgba(255, 255, 255, 0.6);
  width: 300px;
  height: 200px;
  line-height: 200px;
  border-radius: 20px;
  text-align: center;
}
</style>
