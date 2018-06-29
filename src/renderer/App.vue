<template>
  <div id="app">
    <div id="book" >
      <div class="page" ref="page">
        <div class="content" v-html="currentContent" ></div>
      </div>
    </div>
    <div class="menu">
      <div @click="current -= 1">上一章</div>
      <div>{{current}}/{{total}}</div>
      <div @click="current += 1">下一章</div>
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
      current: 1
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
  methods: {
  },
  watch: {
    current (now) {
      this.$refs.page.scrollTop = 0
    }
  },
  mounted () {
    const book = document.getElementById('book')
    console.log(book)
    book.ondragover = function (e) {
      event.preventDefault()
      event.stopPropagation()
      book.querySelector('.page').style.border = '2px dashed #fff'
    }
    book.ondragleave = function (e) {
      event.preventDefault()
      event.stopPropagation()
      book.querySelector('.page').style.border = '2px solid #fff'
    }
    book.ondrop = (event) => {
      event.preventDefault()
      event.stopPropagation()
      console.log(event)
      var files = this.files || event.dataTransfer.files
      var reader = new FileReader()
      reader.onload = (event) => {
        var file_content = event.target.result
        let mobiFile = new MobiFile(file_content)
        mobiFile.render()
        console.log(mobiFile.pages)
        this.pages = mobiFile.pages
      }
      reader.readAsArrayBuffer(files[0])
      // ipcRenderer.send('ondragstart', '/path/to/item')
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

</style>
