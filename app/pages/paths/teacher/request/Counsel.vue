<script>
import { format } from 'date-fns'
import koLocale from 'date-fns/locale/ko'
import ContentWrapper from '@/components/ContentWrapper.vue'
import * as counsel from '@/src/api/counsel'

export default {
  name: 'Counsel',

  components: { ContentWrapper },

  filters: {
    filterTime (time) {
      return format(time, 'dddd ( MM월 DD일 ) hh시 mm분 ~', {
        locale: koLocale
      })
    },

    filterEndTime (time) {
      return format(time, 'hh시 mm분')
    }
  },
  data () {
    return {
      pending: false,
      list: []
    }
  },

  async created () {
    this.refresh()
  },

  methods: {
    async refresh () {
      this.pending = true
      this.list = await counsel.getAdminCounsel()
      this.pending = false
    }
  }
}
</script>

<template lang="html">
  <content-wrapper>
    <h1 slot="header">
      <span class="icon-comment" />상담 신청 관리
      <span
        class="counsel__excel"
        onclick="location.href='http://dev-api.dimigo.in/counsel/excel'"
      >
        <span class="icon-long-arrow-down" />엑셀 다운로드
      </span>
    </h1>

    <dimi-card
      slot="main"
      class="counsel__main"
    >
      <div
        v-if="pending"
        class="counsel__loader-wrapper"
      >
        <dimi-loader />
      </div>

      <template v-else>
        <div
          v-for="(counsel, index) in list"
          :key="`counsel-${index}`"
        >
          <div class="counsel__list">
            <span class="counsel__title">
              {{ counsel.startDate | filterTime }}{{ counsel.endDate | filterEndTime }}
            </span>
            <div class="counsel__speaker">
              <template v-if="counsel.caniapplied === 'no'">
                {{ counsel.request[0].user.serial }} {{ counsel.request[0].user.name }}
              </template>
              <span v-else>
                신청자 없음
              </span>
            </div>
          </div>
        </div>
      </template>
    </dimi-card>
  </content-wrapper>
</template>

<style lang="scss" scoped>
.counsel {
  &__loader-wrapper {
    display: flex;
    height: 35vh;
    align-items: center;
    justify-content: center;
  }

  &__main {
    padding-top: 0;
  }

  &__excel {
    margin-top: 1em;
    margin-right: 0.5em;
    color: $aloes;
    cursor: pointer;
    float: right;
    font-size: 16px;
  }

  &__list {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    padding: 24px;
    border-bottom: 1px solid $gray-lighter;
    cursor: pointer;
  }

  &__title {
    flex: 1;
    margin-right: 16px;
    color: $black !important;
    font-size: 16px;
    font-weight: $font-weight-bold;
  }

  &__item {
    margin-right: 16px;
    color: $gray;
    font-size: 16px;
    font-weight: $font-weight-bold;
  }

}
</style>
