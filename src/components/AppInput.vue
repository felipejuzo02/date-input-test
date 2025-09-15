<template>
  <q-input
    ref="input"
    v-model="model"
    v-bind="$attrs"
    inputmode="numeric"
    label="Teste"
    :mask="currentMask"
    :unmasked-value="unmaskedValue"
  >
    <template v-if="icon" #prepend>
      <q-icon :name="icon" size="xs" />
    </template>

    <template v-for="(_, name) in $slots" #[name]="context">
      <slot :name="name" v-bind="context || {}" />
    </template>
  </q-input>
</template>

<script>
const Masks = {
  CompanyDocument: 'company-document',
  Document: 'document',
  PersonalDocument: 'personal-document',
  Phone: 'phone',
  PostalCode: 'postal-code',
}

export default {
  name: 'QasInput',

  inheritAttrs: false,

  props: {
    mask: {
      type: String,
      default: '',
    },

    modelValue: {
      default: '',
      type: [String, Number],
    },

    unmaskedValue: {
      default: true,
      type: Boolean,
    },

    icon: {
      type: String,
      default: '',
    },

    iconRight: {
      type: String,
      default: '',
    },
  },

  emits: ['update:modelValue'],

  data() {
    return {
      currentMask: '',
      inputReference: null,
    }
  },

  computed: {
    masks() {
      return {
        [Masks.CompanyDocument]: () => '##.###.###/####-##',
        [Masks.Document]: () => this.toggleMask('###.###.###-###', '##.###.###/####-##'),
        [Masks.PersonalDocument]: () => '###.###.###-##',
        [Masks.Phone]: () => this.toggleMask('(##) ####-#####', '(##) #####-####'),
        [Masks.PostalCode]: () => '#####-###',
      }
    },

    model: {
      get() {
        return this.modelValue
      },

      set(value) {
        return this.$emit('update:modelValue', value)
      },
    },
  },

  watch: {
    currentMask(value) {
      if (!value) return

      const input = this.getInput()

      requestAnimationFrame(() => {
        input.selectionStart = input.value ? input.value.length : ''
      })
    },

    modelValue: {
      handler() {
        this.handleMask()
      },
      immediate: true,
    },

    error: {
      handler(value) {
        this.errorData = value
      },

      immediate: true,
    },
  },

  mounted() {
    this.inputReference = this.$refs.input
  },

  methods: {
    focus() {
      return this.inputReference.focus()
    },

    resetValidation() {
      return this.inputReference.resetValidation()
    },

    toggleMask(first, second) {
      const length = first.split('#').length - 2
      return this.modelValue?.length > length ? second : first
    },

    validate(value) {
      return this.inputReference.validate(value)
    },

    onPaste(event) {
      if (!this.currentMask) return

      const value = event.clipboardData.getData('text')
      const input = this.getInput()

      requestAnimationFrame(() => {
        this.$emit('update:modelValue', value)
        input.selectionStart = input.value ? input.value.length : ''
      })
    },

    getInput() {
      return this.inputReference?.$el?.querySelector('input')
    },

    handleErrors() {
      if (this.useRemoveErrorOnType && this.error) {
        this.errorData = false
      }
    },

    handleMask() {
      if (!this.modelValue?.length) return

      const hasDefaultMask = Object.prototype.hasOwnProperty.call(this.masks, this.mask)

      this.$nextTick(() => {
        this.currentMask = hasDefaultMask ? this.masks[this.mask]() : this.mask
      })
    },
  },
}
</script>
