<template>
  <div class="py-8 space-y-1 bg-light">
    <div class="flex justify-center ">
      <div
        class="flex items-center justify-center w-20 h-20 bg-white rounded-full "
      >
        <TIcon :name="n.iconName" />
      </div>
    </div>
    <h3 class="text-2xl font-extrabold text-center">{{ title }}</h3>
    <p class="text-center">{{ description }}</p>
  </div>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    description: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      n: {
        iconName: this.name,
      },
    }
  },
}
</script>
