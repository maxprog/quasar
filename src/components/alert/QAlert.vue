<template>
  <div :class="containerClass" class="q-alert-container">
    <q-transition
      :name="name"
      :enter="enter"
      :leave="leave"
      :duration="duration"
      :appear="appear"
      @after-leave="$emit('dismiss-end')"
    >
      <div
        v-if="active"
        class="q-alert row no-wrap"
        :class="classes"
      >
        <div class="q-alert-icon row col-auto flex-center">
          <q-icon :name="alertIcon"></q-icon>
        </div>
        <div class="q-alert-content col self-center">
          <slot></slot>
          <div
            v-if="actions && actions.length"
            class="q-alert-actions row items-center"
          >
            <span
              v-for="btn in actions"
              :key="btn.label"
              @click="dismiss(btn.handler)"
              v-html="btn.label"
              class="uppercase"
            ></span>
          </div>
        </div>
        <div
          v-if="dismissible"
          class="q-alert-close self-top col-auto"
        >
          <q-icon
            name="close"
            class="cursor-pointer"
            @click="dismiss"
          ></q-icon>
        </div>
      </div>
    </q-transition>
  </div>
</template>

<script>
import typeIcon from '../../utils/type-icons'
import { QIcon } from '../icon'
import { QTransition } from '../transition'

export default {
  name: 'q-alert',
  components: {
    QIcon,
    QTransition
  },
  props: {
    value: Boolean,
    duration: Number,
    name: String,
    enter: String,
    leave: String,
    appear: Boolean,
    color: {
      type: String,
      default: 'negative'
    },
    inline: Boolean,
    icon: String,
    dismissible: Boolean,
    actions: Array,
    position: {
      type: String,
      validator: v => [
        'top', 'top-right', 'right', 'bottom-right',
        'bottom', 'bottom-left', 'left', 'top-left',
        'top-center', 'bottom-center'
      ].includes(v)
    }
  },
  data () {
    return {
      active: true
    }
  },
  watch: {
    value (v) {
      if (v !== this.active) {
        this.active = v
        if (!v) {
          this.$emit('dismiss')
        }
      }
    }
  },
  computed: {
    alertIcon () {
      return this.icon || typeIcon[this.color] || typeIcon.warning
    },
    containerClass () {
      const cls = []
      let pos = this.position

      if (pos) {
        if (pos.indexOf('center') > -1) {
          cls.push('row justify-center')
          pos = pos.split('-')[0]
        }
        cls.push(`fixed-${pos}${pos === 'left' || pos === 'right' ? ' row items-center' : ''} z-alert`)
      }
      if (this.inline) {
        cls.push('inline-block')
      }
      return cls
    },
    classes () {
      return `shadow-2 bg-${this.color}`
    }
  },
  methods: {
    dismiss (fn) {
      this.active = false
      this.$emit('input', false)
      this.$emit('dismiss')
      if (typeof fn === 'function') {
        fn()
      }
    }
  }
}
</script>
