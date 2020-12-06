<template>
  <div>
    <div class="mt-4">
      <TLoader v-if="loading" />
      <div v-else-if="!count">
        No scheduled events
      </div>
      <div>
        <div v-for="(items, date) in itemsByDate" :key="date" class="mb-8">
          <h2 class="font-bold bg-dark text-white py-2 px-4 rounded">
            {{ getDay(date) }}, {{ getDate(date) }}
          </h2>
          <div v-for="item in items" :key="item.id" class="px-4 mt-4 flex">
            <div v-if="false" class="mr-2 flex justify-center items-start pt-1">
              <button
                v-if="item.response === 'up'"
                class="text-green-500"
                @click="updateRsvp(item.id, 'events', 'down')"
              >
                <TIcon name="check_circle" class="w-4 h-4" />
              </button>
              <button v-else @click="updateRsvp(item.id, 'events', 'up')">
                <TIcon name="check" class="w-4 h-4" />
              </button>
            </div>
            <div class="mr-2">
              {{ getTime(item.startDate) }}
            </div>
            <div class="mr-2">
              {{ item.type === 'Party' ? '🎉' : '👣' }}
            </div>
            <div>
              <router-link
                :to="`/events/${item.id}`"
                class="font-bold leading-none hover:underline hover:text-primary"
              >
                {{ item.name }}
              </router-link>
              <div class="text-xs flex flex-wrap">
                <div v-if="item.organiser" class="hidden md:block">
                  <div class="flex items-center mr-2">
                    <TIcon name="store" class="w-4 h-4 mr-1" />
                    <p>
                      {{ item.organiser }}
                    </p>
                  </div>
                </div>
                <div v-if="item.address">
                  <div class="flex items-center">
                    <TIcon name="place" class="w-4 h-4 mr-1" />
                    <p>
                      {{ item.address }}
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, ref } from '@nuxtjs/composition-api'
import { startOfWeek, addDays, endOfYear } from 'date-fns'
import useRSVP from '~/use/rsvp'
import useCollection from '~/use/collection'
import useAccounts from '~/use/accounts'
import useAuth from '~/use/auth'
import useCities from '~/use/cities'
import useRouter from '~/use/router'
import {
  dateDiff,
  sortBy,
  getTime,
  getDate,
  getDay,
  getYmd,
  getDateObect
} from '~/utils'

export default {
  name: 'TEventList',
  props: {
    filter: {
      type: Object,
      default: null
    }
  },
  setup(params) {
    const {
      getCount,
      getRsvpResponse,
      updateRsvp,
      loading: loadingRsvps
    } = useRSVP()
    const { currentCity } = useCities()
    const { docs, loading: loadingPosts, getById } = useCollection(
      'events',
      params.filter
    )

    const { uid } = useAuth()

    const map = (item) => {
      if (!item.id) {
        return {}
      }

      const upVotes = getCount(item.id, 'up')
      const downVotes = getCount(item.id, 'down')
      const votes = upVotes - downVotes
      const response = getRsvpResponse(item.id)
      const multi = !response ? 3 : response === 'up' ? 2 : 1
      const order = multi * 100 + votes
      const startDate = getDateObect(item.startDate)

      return {
        ...item,
        startDate,
        upVotes,
        downVotes,
        votes,
        response,
        order
      }
    }

    const now = new Date()
    const startOfWeekDate = startOfWeek(now, { weekStartsOn: 1 })
    const startOfWeekString = getYmd(startOfWeekDate)
    const startOfTodayString = getYmd(now)
    const endOfWeekString = getYmd(addDays(startOfWeekDate, 7))
    const in7DaysString = getYmd(addDays(now, 7))
    const endOfYearString = getYmd(endOfYear(now))

    const count = computed(() => items.value.length)
    const { route } = useRouter()

    const loading = computed(() => loadingRsvps.value && loadingPosts.value)

    const { getAccount } = useAccounts()

    const activeFilter = ref('next7days')

    const isPublic = (item) => item.visibility !== 'Unlisted'

    const filterOptions = computed(() => [
      {
        value: 'next7days',
        label: 'Next 7 days',
        filter: (item) =>
          getYmd(item.startDate) >= startOfTodayString &&
          getYmd(item.startDate) <= in7DaysString &&
          isPublic(item)
      },
      {
        value: 'thisYear',
        label: 'This Year',
        filter: (item) =>
          getYmd(item.startDate) >= startOfTodayString &&
          getYmd(item.startDate) <= endOfYearString &&
          isPublic(item)
      },
      {
        value: 'createdByMe',
        label: 'Created by me',
        filter: (item) => item.createdBy === uid.value
      },
      {
        value: 'schedule',
        label: 'My schedule',
        filter: (item) =>
          item.response === 'up' && getYmd(item.startDate) >= startOfWeekString
      }
    ])

    const activeFilterItem = computed(() =>
      filterOptions.value.find((item) => item.value === activeFilter.value)
    )

    const thisCityFilter = (item) => item.city === currentCity.value

    const items = computed(() => {
      let result = docs.value.map(map)

      if (!route.query.all) {
        result = result
          .filter(thisCityFilter)
          .filter(activeFilterItem.value.filter)
      }

      return result.sort(sortBy('startDate'))
    })

    const itemsByDate = computed(() => {
      const result = {}
      items.value.forEach((item) => {
        const date = getYmd(item.startDate)

        result[date] = result[date] || []
        result[date].push(item)
      })

      return result
    })

    return {
      currentCity,
      count,
      itemsByDate,
      getRsvpResponse,
      updateRsvp,
      dateDiff,
      getAccount,
      loading,
      getById,
      uid,
      getTime,
      getDay,
      getDate,
      startOfWeekString,
      endOfWeekString,
      activeFilter,
      filterOptions
    }
  }
}
</script>