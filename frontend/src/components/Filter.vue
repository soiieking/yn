<template>
  <XMask :show="!!show" @close="show = false">
    <QuickOpen @choose-file="show" @close="show = false" :with-marked="show && show.withMarked"></QuickOpen>
  </XMask>
</template>

<script>
import { mapState } from 'vuex'
import XMask from './Mask'
import QuickOpen from './QuickOpen'
import { encodeMarkdownLink } from '@/lib/utils'

export default {
  name: 'x-filter',
  components: { QuickOpen, XMask },
  data () {
    return {
      show: false,
    }
  },
  mounted () {
    window.addEventListener('keydown', this.keydownHandler, true)
  },
  beforeDestroy () {
    window.removeEventListener('keydown', this.keydownHandler)
  },
  methods: {
    keydownHandler (e) {
      if (e.key === 'i' && e.ctrlKey && e.altKey) {
        this.show = f => {
          if (this.currentFile) {
            const relativePath = f.path.replace(this.currentFile.path.substr(0, this.currentFile.path.lastIndexOf('/')), '.')
            this.$bus.emit('editor-insert-value', `[${f.name.replace(/\.[^.]+$/, '')}](${encodeMarkdownLink(relativePath)})`)
          }
          this.show = false
        }
        this.show.withMarked = false
        e.preventDefault()
        e.stopPropagation()
      } else if (e.key === 'p' && e.ctrlKey) {
        this.show = f => {
          this.$store.commit('app/setCurrentFile', f)
          this.show = false
        }
        this.show.withMarked = true
        e.preventDefault()
        e.stopPropagation()
      }
    },
  },
  computed: {
    ...mapState('app', ['currentFile'])
  }
}
</script>
