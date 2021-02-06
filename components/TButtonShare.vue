<template>
  <div class="flex justify-end mb-4">
    <TButton
      icon="share"
      class="rounded-full cursor-pointer hover:bg-gray-200 p-1"
      @click="share()"
    />
    <TPopup v-if="sharing" title="Share" @close="sharing = false">
      <div class="w-64">
        <TButton type="nav" @click="copyToClipboard">
          Copy Link
        </TButton>
        <TButton
          :href="`whatsapp://send?text=${text} ${url}`"
          data-action="share/whatsapp/share"
          type="nav"
        >
          Whatsapp
        </TButton>
        <TButton
          :href="`https://t.me/share/url?url=${url}&text=${text}`"
          target="_blank"
          rel="nofollow noreferrer"
          type="nav"
        >
          Telegram
        </TButton>
        <TButton
          :href="
            `https://www.facebook.com/share.php?display=page&u=${url}&t=${text}`
          "
          target="_blank"
          rel="nofollow noreferrer"
          type="nav"
        >
          Facebook
        </TButton>
        <TButton
          :href="`https://twitter.com/intent/tweet?text=${text} ${url}`"
          target="_blank"
          rel="nofollow noreferrer"
          type="nav"
        >
          Twitter
        </TButton>
      </div>
    </TPopup>
  </div>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      default: ''
    },
    url: {
      type: String,
      default: ''
    },
    file: {
      type: String,
      default: ''
    },
    fileName: {
      type: String,
      default: ''
    }
  },
  data: () => ({
    sharing: false
  }),
  methods: {
    async copyToClipboard() {
      await navigator.clipboard.writeText(this.url)
      this.sharing = false
      this.$toast.success('Link copied to clipboard')
    },
    async share() {
      const response = await fetch(this.file)
      const blob = await response.blob()
      const file = new File([blob], `${this.fileName}.png`, {
        type: 'image/png'
      })

      const filesArray = [file]

      if (
        !navigator.share ||
        !navigator.canShare ||
        !navigator.canShare({ files: filesArray })
      ) {
        this.sharing = true

        return
      }

      const shareData = {
        title: this.text,
        url: this.url,
        files: filesArray
      }

      navigator.share(shareData)
    }
  }
}
</script>