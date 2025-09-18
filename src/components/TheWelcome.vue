<template>
  <div class="editor-wrap">
    <!-- Toolbar -->
    <div class="toolbar">
      <button @click="exec('bold')"><b>B</b></button>
      <button @click="exec('italic')"><i>I</i></button>
      <button @click="exec('underline')"><u>U</u></button>
      <button @click="exec('insertUnorderedList')">• List</button>
      <button @click="exec('insertOrderedList')">1. List</button>
      <button @click="insertLink">🔗 Link</button>
      <button @click="exec('removeFormat')">Clear</button>
    </div>

    <!-- Rich text editor -->
    <div
      ref="editor"
      class="rich-editor"
      contenteditable="true"
      placeholder="Type something here..."
    ></div>

    <!-- Action buttons -->
    <div class="actions">
      <button @click="getHtml">Get HTML</button>
    </div>

    <!-- Output -->
    <div v-if="rawHtml" class="output">
      <h4>HTML to save to database</h4>
      <textarea readonly class="html-box" :value="rawHtml"></textarea>
      <button class="copy-btn" @click="copyHtml">Copy HTML</button>
      <div v-if="copied" class="copied-msg">✅ Copied!</div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const editor = ref(null)
const rawHtml = ref('')
const copied = ref(false)

function exec(command) {
  editor.value?.focus()
  document.execCommand(command, false, null)
}

function insertLink() {
  const url = prompt('Enter URL (include https://)', 'https://')
  if (url) {
    editor.value?.focus()
    document.execCommand('createLink', false, url)
  }
}

function getHtml() {
  rawHtml.value = editor.value?.innerHTML.trim() || ''
  copied.value = false
}

async function copyHtml() {
  await navigator.clipboard.writeText(rawHtml.value)
  copied.value = true
  setTimeout(() => (copied.value = false), 1500)
}
</script>

<style scoped>
.editor-wrap {
  max-width: 700px;
  margin: 1rem auto;
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, Arial;
}

.toolbar {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 0.6rem;
  flex-wrap: wrap;
}

.toolbar button {
  padding: 0.35rem 0.6rem;
  border: 1px solid #ddd;
  background: #f7f7f7;
  border-radius: 6px;
  cursor: pointer;
}

.toolbar button:hover { background: #eee; }

.rich-editor {
  min-height: 160px;
  border: 1px solid #ddd;
  padding: 0.75rem;
  border-radius: 8px;
  background: #fff;
  outline: none;
  overflow-y: auto;
}

.rich-editor:empty:before {
  content: attr(placeholder);
  color: #aaa;
  pointer-events: none;
}

.actions button {
  margin-top: 0.8rem;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  border: none;
  background: #2563eb;
  color: white;
  cursor: pointer;
}

.output {
  margin-top: 1rem;
  border-top: 1px dashed #ccc;
  padding-top: 1rem;
}

.html-box {
  width: 100%;
  min-height: 120px;
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 0.6rem;
  font-family: monospace;
  white-space: pre-wrap;
  background: #fdfdfd;
}

.copy-btn {
  margin-top: 0.5rem;
  padding: 0.4rem 0.9rem;
  border: none;
  border-radius: 6px;
  background: #10b981;
  color: white;
  cursor: pointer;
}

.copied-msg {
  margin-top: 0.3rem;
  color: #16a34a;
  font-size: 0.9rem;
}
</style>
