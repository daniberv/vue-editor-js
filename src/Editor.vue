<template>
  <div :id="holder" />
</template>

<script>
import {
  reactive,
  onMounted,
  watch,
  defineComponent
} from '@vue/composition-api'
import EditorJS from '@editorjs/editorjs'

export default defineComponent({
  name: 'vue-editor-js',
  props: {
    holder: {
      type: String,
      default: () => 'vue-editor-js',
      require: true
    },
    config: {
      type: Object,
      default: () => ({}),
      require: true
    },
    initialized: {
      type: Function,
      default: () => {}
    }
  },
  setup: (props, context) => {
    const state = reactive({ editor: null })

    function initEditor(props) {
      destroyEditor()
      state.editor = new EditorJS({
        holder: 'vue-editor-js',
        ...props.config
      })
      props.initialized(state.editor)
    }

    function destroyEditor() {
      if (state.editor) {
        state.editor.destroy()
        state.editor = null
      }
    }
    
    function save() {
      if (!state.editor) {
        return
      }
      state.editor.save()
        .then((result) => context.emit('save', result))
        .catch((err) => console.error(err))
    }

    onMounted(_ => initEditor(props))

    return { props, state }
  }
})
</script>
