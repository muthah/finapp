<script>
import Button from '@/components/shared/button/Button'
import DashboardFilter from '@/components/dashboard/DashboardFilter'
import DashboardNav from '@/components/dashboard/DashboardNav'
import DashboardStatControl from '@/components/dashboard/DashboardStatControl'
import EmptyData from '@/components/shared/emptyData/EmptyData'
import StatChartDonut from '@/components/stat/StatChartDonut'
import StatChartsLine from '@/components/stat/StatChartsLine'
import StatPc from '@/components/stat/StatPc'
import StatSummaryPc from '@/components/stat/StatSummaryPc'
import TrnsList from '@/components/trns/list/TrnsList'

export default {
  components: {
    Button,
    DashboardFilter,
    DashboardNav,
    DashboardStatControl,
    EmptyData,
    StatChartDonut,
    StatChartsLine,
    StatPc,
    StatSummaryPc,
    TrnsList
  },

  data () {
    return {
      visiblePeriodMenu: false
    }
  },

  computed: {
    isEmptyData () {
      const statCurrentPeriod = this.$store.getters.statCurrentPeriod
      if (statCurrentPeriod.incomes.categoriesIds.length === 0 &&
          statCurrentPeriod.expenses.categoriesIds.length === 0 &&
          this.$store.getters.selectedTrnsIdsWithDate.length === 0) {
        return true
      }
      return false
    }
  }
}
</script>

<template lang="pug">
.dashboard(v-show="$store.state.ui.activeTab === 'stat'")
  StatChartsLine(v-show="$store.state.dashboard.activeTab !== 'balance'")
  DashboardNav
  StatSummaryPc
  DashboardFilter
  DashboardStatControl

  template(v-if="!$store.getters.hasTrns")
    .startSomething
      .options__item
        Button._grey._center(
          :title="$lang.createTrn"
          v-on:onClick="$store.dispatch('openTrnForm', { action: 'create' })")

      .options__item(v-if="$store.state.demo.hasDemo")
        Button._grey._center(
          :title="$lang.welcome.demo.btn"
          v-on:onClick="$store.dispatch('createDemo')")

  .dashboard__content(v-if="$store.getters.hasTrns")
    //- empty
    //------------------------------------------------
    template(v-if="isEmptyData")
      transition(name="animation-tab")
        .dashboard__wrap
          .dashboard__tab(v-show="$store.state.dashboard.activeTab === 'stat'")
            EmptyData(text="No stat for this period")

    //- stat & history
    template(v-else)
      transition(name="animation-tab")
        .dashboard__wrap
          .dashboard__tab(v-show="$store.state.dashboard.activeTab === 'stat'")
            StatPc

      transition(name="animation-tab")
        .dashboard__wrap
          .dashboard__tab._trns(v-show="$store.state.dashboard.activeTab === 'history'")
            TrnsList(:size="50" :key="$store.state.filter.date")

    transition(name="animation-tab")
      .dashboard__tab(v-show="$store.state.dashboard.activeTab === 'balance'")
        StatChartDonut(v-if="$store.getters.hasTrns")
</template>

<style lang="stylus" scoped>
@import "~@/stylus/variables/margins"
@import "~@/stylus/variables/media"
@import "~@/stylus/variables/scrollbar"

.dashboard
  overflow hidden
  overflow-y scroll
  scrollbar()

  &__wrap
    position relative
    max-width 1100px
    padding 0px 60px

  &__content
    position relative

  &__tab
    position absolute
    left 0
    top 0
    width 100%
    padding-bottom 60px

    &._trns
      padding 60px 60px
      padding-top 30px

.startSomething
  padding 20px
  display flex
  flex-flow column
  align-items center
  justify-content center

  .options__item
    margin-bottom 40px
</style>
