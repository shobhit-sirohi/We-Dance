<template>
  <div class="min-h-screen font-noto bg-dark">
    <main>
      <div class="relative">
        <div class="absolute inset-x-0 bottom-0 bg-gray-100 h-1/2" />
        <div>
          <div class="relative sm:overflow-hidden">
            <div class="absolute inset-0">
              <img
                class="object-cover w-full h-full"
                alt="People dancing bachata"
                src="/img/hero_640.jpg"
                sizes="(max-width: 640px) 100vw, (max-width: 768px) 100vw, (max-width: 1024px) 100vw, (max-width: 1280px) 100vw, (max-width: 1536px) 100vw, 100vw"
                srcset="
                  /img/hero_640.jpg   640w,
                  /img/hero_768.jpg   768w,
                  /img/hero_1024.jpg 1024w,
                  /img/hero_1280.jpg 1280w,
                  /img/hero_1536.jpg 1536w,
                  /img/hero_1536.jpg 1536w
                "
              />
              <div class="absolute inset-0 bg-black opacity-50" />
            </div>

            <nav class="relative flex justify-end p-4">
              <TButton type="simple" to="/signin" :label="$t('signin')" />
            </nav>

            <div class="relative p-4 sm:p-10 lg:p-14">
              <div class="flex justify-center">
                <TIcon name="logo-vertical-white" />
              </div>

              <p
                class="max-w-lg mx-auto mt-2 text-center text-white sm:max-w-3xl"
              >
                Even one dance contact in a city makes a difference
              </p>

              <div class="max-w-lg mx-auto">
                <TButton
                  icon="search"
                  label="Search dancers"
                  to="/search"
                  type="void"
                  class="w-full px-4 py-2 mt-8 font-semibold text-gray-500 bg-white border border-gray-400 rounded-full shadow hover:bg-gray-100"
                />
              </div>

              <div class="flex justify-center">
                <div class="flex mt-8 space-x-4 font-bold text-white">
                  <div class="flex flex-col items-center justify-center">
                    <TIcon name="group" class="w-16 h-16" />
                    <div class="text-4xl">1309</div>
                    <div class="text-sm">dancers</div>
                  </div>
                  <div class="flex flex-col items-center justify-center">
                    <TIcon name="location" class="w-16 h-16" />
                    <div class="text-4xl">102</div>
                    <div class="text-sm">cities</div>
                  </div>
                  <div class="flex flex-col items-center justify-center">
                    <TIcon name="calendar" class="w-16 h-16" />
                    <div class="text-4xl">441</div>
                    <div class="text-sm">events</div>
                  </div>
                </div>
              </div>

              <div class="flex justify-center mt-8">
                <TButton
                  type="primary"
                  to="/register"
                  label="Join the community"
                />
              </div>

              <p
                class="max-w-lg mx-auto mt-8 text-center text-white sm:max-w-3xl"
              >
                Salsa, Bachata, Kizomba, Urban Kiz, Raggaeton, Casino, Tango and
                130 more dance styles
              </p>
            </div>
          </div>
          <div class="p-4 bg-light">
            <div class="md:grid md:grid-cols-2 lg:grid-cols-3 md:gap-4">
              <TFeature
                name="icon-calendar"
                title="Event Calendar"
                description="Find dance workshops, courses, socials and parties. We fill event calendar all together as a community. Everyone can share."
              />
              <TFeature
                name="icon-partner"
                title="Dance Partners"
                description="Find dance partner for any dance style. Let us know who you are looking for and let them find you."
              />
              <TFeature
                name="icon-qa"
                title="City Chats"
                description="Find dance chat in any city. Join your city chat and invite your friends to help new dancers in the city. Think globally, act locally."
              />
              <TFeature
                name="icon-festival"
                title="Dance Travel"
                description="Find international dance festivals and congresses. Share a ride, an appartment and find a buddy to experience a new city together."
              />
              <TFeature
                name="icon-collab"
                title="Volunteer"
                description="We build a community, where everyone enters with mindset “I can help”, so whenever you need help you can count on it. We are one big family worldwide. We help each other."
              />
              <TFeature
                name="icon-podcast"
                title="Interviews"
                description="We are going to show you how dancers, artists and organisers can share advice, tips and know how out of all of us."
              />
            </div>
          </div>
        </div>
      </div>
    </main>

    <div class="text-white bg-dark">
      <TFooter class="max-w-2xl p-4 mx-auto" />
    </div>
  </div>
</template>

<script>
import { onMounted, watch } from 'vue-demi'
import { useAuth } from '~/use/auth'
import { useRouter } from '~/use/router'

export default {
  name: 'Index',
  layout: 'empty',
  setup() {
    const { profile } = useAuth()
    const { router } = useRouter()

    watch(profile, (p) => {
      router.push('/feed')
    })

    onMounted(() => {
      if (profile.value?.username) {
        router.push('/feed')
      }
    })
  },
}
</script>
