<template>
  <div class="flex flex-col space-y-2 text-sm">
    <TInputSelect
      v-model="lang"
      :options="localesDropdown"
      class="mb-4 text-dark"
    />
    <p>© WeDance 2021 – All rights reserved.</p>
    <a class="underline hover:no-underline" href="/about" target="_blank">{{
      $t('footer.about')
    }}</a>
    <router-link
      class="underline hover:no-underline"
      to="/support#hs-chat-open"
      target="_blank"
      >Support</router-link
    >
    <router-link class="underline hover:no-underline" to="/privacy"
      >Datenschutzerklärung</router-link
    >
    <router-link class="underline hover:no-underline" to="/terms"
      >Impressum/Terms</router-link
    >
    <router-link class="underline hover:no-underline" to="/coc">{{
      $t('footer.coc')
    }}</router-link>
    <router-link class="underline hover:no-underline" to="/faq">{{
      $t('footer.faq')
    }}</router-link>
  </div>
</template>

<script>
export default {
  computed: {
    lang: {
      get() {
        return this.$i18n.locale
      },
      set(val) {
        this.$i18n.setLocale(val)
      },
    },
    localesDropdown() {
      return this.$i18n.locales.map((locale) => ({
        value: locale.code,
        label: locale.name,
      }))
    },
  },
}
</script>
