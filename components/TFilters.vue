<template>
  <div class="my-2">
    <TForm
      v-model="internalValue"
      :fields="fields"
      class="flex flex-no-wrap space-x-2"
      submit-label=""
    />
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Object,
      default: () => {},
    },
    default: {
      type: Object,
      default: () => {},
    },
    fields: {
      type: Array,
      default: () => [],
    },
  },
  computed: {
    internalValue: {
      get() {
        return this.value
      },
      set(val) {
        this.$emit('input', val)
      },
    },
  },
}
</script>
