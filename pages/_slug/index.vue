<template>
  <div :class="classes">
    <nuxt-content v-if="page" :document="page" />
    <TProfile v-else-if="profile" :profile="profile" />
  </div>
</template>

<script>
export default {
  name: 'Slug',
  async asyncData({ app, $content, params, error }) {
    const slug = params.slug

    let page = null
    let profile = null
    let pageFound = false
    let profileFound = false

    try {
      page = await $content(slug).fetch()
      pageFound = true
    } catch (e) {}

    if (!pageFound) {
      const collection = await app.$fire.firestore
        .collection('profiles')
        .where('username', '==', slug)
        .get()

      if (collection.docs.length > 0) {
        const doc = collection.docs[0]

        profile = doc.data()
        profile.id = doc.id

        profileFound = true
      }
    }

    if (!pageFound && !profileFound) {
      error({ statusCode: 404 })
    }

    return {
      page,
      profile,
    }
  },
  computed: {
    classes() {
      let classes = ''

      if (this.page) {
        classes += this.page.container + ' p-4 '
        classes += this.page.notypo ? '' : 'typo'
      }

      return classes
    },
  },
  head() {
    if (this.profile) {
      const profile = this.profile

      return {
        title: profile.username,
        canonical: `/${profile.username}`,
        meta: [
          {
            hid: 'description',
            name: 'description',
            content: profile.bio,
          },
          {
            property: 'og:image',
            content: profile.socialCover || profile.photo,
            hid: 'og:image',
          },
          {
            property: 'og:type',
            content: 'profile',
            hid: 'og:type',
          },
          {
            property: 'og:title',
            content: profile.username,
            hid: 'og:title',
          },
          {
            property: 'og:description',
            content: profile.bio,
            hid: 'og:description',
          },
        ],
      }
    }

    if (this.page) {
      const page = this.page

      return {
        title: page.title,
        canonical: `/${page.slug}`,
        meta: [
          {
            hid: 'description',
            name: 'description',
            content: page.description,
          },
          {
            property: 'og:type',
            content: 'article',
            hid: 'og:type',
          },
          {
            property: 'og:title',
            content: page.title,
            hid: 'og:title',
          },
          {
            property: 'og:description',
            content: page.description,
            hid: 'og:description',
          },
        ],
      }
    }
  },
}
</script>
