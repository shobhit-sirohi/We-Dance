<template>
  <main class="space-y-4">
    <TButton label="← Offers" to="/offers" type="link" />
    <TOffer class="border rounded space-y-2" :offer="doc" />
  </main>
</template>

<script>
import { loadDoc } from '~/utils'

export default {
  name: 'OfferView',
  async asyncData(ctx) {
    return await loadDoc(ctx, 'offers')
  },
}
</script>
