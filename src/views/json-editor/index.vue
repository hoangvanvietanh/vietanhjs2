<template>
  <div>
    <el-tabs type="border-card">
      <el-tab-pane label="Editor">
        <el-tabs v-model="editableTabsValue" type="card" editable @edit="handleTabsEdit">
          <el-tab-pane
            v-for="(item, index) in editableTabs"
            :key="item.name"
            :label="item.title"
            :name="item.name"
          >
            <div :id="item.name" style="height: 70vh" />
          </el-tab-pane>
        </el-tabs>
      </el-tab-pane>
      <el-tab-pane label="Diff">
        <el-row :gutter="20">
          <el-col :span="7">
            <el-input
              v-model="textDiff1"
              type="textarea"
              :rows="50"
              style="height: 70vh"
              placeholder="Nội dung 1"
            />
          </el-col>
          <el-col :span="7">
            <el-input
              v-model="textDiff2"
              type="textarea"
              :rows="50"
              style="height: 70vh"
              placeholder="Nội dung 2"
            />
          </el-col>
          <el-col :span="10">
            <el-select v-model="value" filterable placeholder="Select" style="width: 100%">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
            <pre id="display" style="height: 70vh;overflow: auto" />
          </el-col>
        </el-row>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import { diffChars, diffLines, diffArrays, diffJson, diffWords, diffWordsWithSpace, diffTrimmedLines } from 'diff'
import JSONEditor from 'jsoneditor'
import 'jsoneditor/dist/jsoneditor.css'
import { Splitpanes, Pane } from 'splitpanes'
import 'splitpanes/dist/splitpanes.css'

export default {
  name: 'JsonEditor',
  components: { Splitpanes, Pane },
  data() {
    return {
      options: [{
        value: 'diffChars',
        label: 'So sánh theo ký tự'
      }, {
        value: 'diffLines',
        label: 'So sánh theo dòng'
      }, {
        value: 'diffArrays',
        label: 'So sánh theo mảng'
      }, {
        value: 'diffJson',
        label: 'So sánh theo chuỗi JSON'
      }, {
        value: 'diffWords',
        label: 'So sánh theo từ'
      }, {
        value: 'diffWordsWithSpace',
        label: 'So sánh theo từ có khoảng trắng'
      }, {
        value: 'diffTrimmedLines',
        label: 'So sánh theo dòng đã xóa khoảng trắng'
      }],
      value: 'diffChars',
      textDiff1: '',
      textDiff2: '',
      editableTabsValue: '0',
      editableTabs: [{
        title: 'Editor',
        name: '0',
        content: 'Tab 1 content',
        isCreated: true
      }],
      tabIndex: 0,
      objectDiff1: '',
      objectDiff2: ''
    }
  },
  watch: {
    textDiff1() {
      this.diffProcess(this.textDiff1, this.textDiff2, this.value)
    },
    textDiff2() {
      this.diffProcess(this.textDiff1, this.textDiff2, this.value)
    },
    value() {
      this.diffProcess(this.textDiff1, this.textDiff2, this.value)
    }
  },
  mounted() {
    this.addEditor('0')
    // this.addEditor('diff1')
    // this.addEditor('diff2')
  },
  updated() {
    this.editableTabs.forEach(x => {
      if (x.name === this.editableTabsValue.toString() && !x.isCreated) {
        this.addEditor(this.editableTabsValue.toString())
        x.isCreated = true
      }
    })
  },
  methods: {
    diffProcess(text1, text2, key) {
      const one = text1
      const other = text2
      const color = ''

      let span = null
      let diff = {}
      switch (key) {
        case 'diffChars':
          diff = diffChars(one, other)
          break
        case 'diffLines':
          diff = diffLines(one, other)
          break
        case 'diffArrays':
          diff = diffArrays(one, other)
          break
        case 'diffJson':
          diff = diffJson(one, other)
          break
        case 'diffWords':
          diff = diffWords(one, other)
          break
        case 'diffWordsWithSpace':
          diff = diffWordsWithSpace(one, other)
          break
        case 'diffTrimmedLines':
          diff = diffTrimmedLines(one, other)
          break
        default:
          diff = diffChars(one, other)
      }

      const display = document.getElementById('display')
      const fragment = document.createDocumentFragment()

      display.innerHTML = ''
      diff.forEach((part) => {
        // green for additions, red for deletions
        // grey for common parts
        const color = part.added ? 'green'
          : part.removed ? 'red' : 'grey'
        span = document.createElement('span')
        span.style.color = color
        span.appendChild(document
          .createTextNode(part.value))
        fragment.appendChild(span)
      })

      display.appendChild(fragment)
    },
    handleTabsEdit(targetName, action) {
      if (action === 'add') {
        const newTabName = ++this.tabIndex + ''
        let size = this.editableTabs.length
        const title = size === 0 ? 'Editor' : 'Editor ' + size++
        this.editableTabs.push({
          title: title,
          name: newTabName,
          content: 'New Tab content',
          isCreated: false
        })
        this.editableTabsValue = newTabName
      }
      if (action === 'remove') {
        const tabs = this.editableTabs
        let activeName = this.editableTabsValue
        if (activeName === targetName) {
          tabs.forEach((tab, index) => {
            if (tab.name === targetName) {
              const nextTab = tabs[index + 1] || tabs[index - 1]
              if (nextTab) {
                activeName = nextTab.name
              }
            }
          })
        }

        this.editableTabsValue = activeName
        this.editableTabs = tabs.filter(tab => tab.name !== targetName)
      }
    },
    addEditor(id) {
      const container = document.getElementById(id)
      const options = {
        modes: ['text', 'code', 'tree', 'form', 'view'],
        mode: 'code'
      }

      const editor = new JSONEditor(container, options)

      editor.set('')
      if (id === 'objectDiff1') {
        this.objectDiff1 = editor
      } else if (id === 'objectDiff2') {
        this.objectDiff2 = editor
      }
      // get json
      // const updatedJson = editor.get()
    }
  }
}
</script>

<style>
.splitpanes {background-color: #f8f8f8;}

.splitpanes__splitter {background-color: #ccc;position: relative;}
.splitpanes__splitter:before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  transition: opacity 0.4s;
  background-color: rgba(255, 0, 0, 0.3);
  opacity: 0;
  z-index: 1;
}
.splitpanes__splitter:hover:before {opacity: 1;}
.splitpanes--vertical > .splitpanes__splitter:before {left: -5px;right: -5px;height: 100%;}
.splitpanes--horizontal > .splitpanes__splitter:before {top: -30px;bottom: -30px;width: 100%;}
</style>
