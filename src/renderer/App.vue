<template>
  <div id="app">
    <file-pond
    name="test"
    ref="pond"
    label-idle="Drop files here..."
    allow-multiple="false"
    accepted-file-types="image/jpeg, image/png"
    v-bind:files="myFiles"
    v-on:init="handleFilePondInit"/>
    <div id="book" class="page" v-html="currentContent"></div>
    <div @click="current -= 1">上一页</div>
    <div>{{current}}/{{total}}</div>
    <div @click="current += 1">下一页</div>
  </div>
</template>

<script>
import MobiFile from './utils/mobi'
// Import Vue FilePond
import vueFilePond from 'vue-filepond'

// Import FilePond styles
import 'filepond/dist/filepond.min.css'

// Create component
const FilePond = vueFilePond()

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
    handleFilePondInit: function () {
      console.log('FilePond has initialized')
      const pond = this.$refs.pond
      pond.$on('addfile', (e, {file}) => {
        console.log('File added', file)
        var reader = new FileReader()
        reader.onload = (event) => {
          var file_content = event.target.result
          let mobiFile = new MobiFile(file_content)
          mobiFile.render()
          this.pages = mobiFile.pages
        }
        reader.readAsArrayBuffer(file)
      })

      // FilePond instance methods are available on `this.$refs.pond`
    }
  },
  mounted () {
  },
  components: {
    FilePond
  }
}
</script>

<style>
#app{
  background-color: #e5e4db
}
#app:after{
  content: '';
  clear: both;
  display: block;
}
.page {
  height: 39em;
  overflow: hidden;
  position: relative;
  float: left;
  height: 48em;
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
</style>
