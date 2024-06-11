<script setup lang="ts">
import GeneralReportCard from '@/components/dashboard/GeneralReportCard.vue'
import { computed, ref, watch } from 'vue'
import { useGlobalUiStore } from '@/stores/ui.store.ts'
import { sleep } from '@/utils/helpers.ts'
import { useThemeConfig } from '@/composables/theme.ts'

/** General Report Cards */
const formatNumber = (number: number) => {
  return new Intl.NumberFormat().format(number)
}

const peopleReachedCategories = ['Week 1', 'Week 2', 'Week 3', 'Week 4', 'Week 5', 'Week 6', 'Week 7', 'Week 8']
const peopleReachedSeriesData = ref([78, 250, 400, 215, 199, 380, 750, 222])
const peopleReachedTotal = computed(() => {
  return formatNumber(peopleReachedSeriesData.value.reduce((partialSum, n) => partialSum + n, 0))
})
const expensesCategories = ['Month 1', 'Month 2', 'Month 3', 'Month 4', 'Month 5']
const expensesSeriesData = ref([13_234, 10_324, 11_999, 27_000, 5_000])
const expensesTotal = computed(() => {
  return formatNumber(expensesSeriesData.value.reduce((partialSum, n) => partialSum + n, 0))
})
const onboardedCategories = ['January', 'February', 'March', 'April', 'May']
const onboardedSeriesData = ref([200, 90, 70, 90, 39])
const onboardedTotal = computed(() => {
  return formatNumber(onboardedSeriesData.value.reduce((partialSum, n) => partialSum + n, 0))
})


/** We Force Update the page to eliminate the delay when hiding the sidebar in desktop view*/
const uiStore = useGlobalUiStore()
const mountCharts = ref(true)
watch(
  () => uiStore.sidebarMinimized,
  async (isMinimized) => {
    if (!isMinimized) {
      mountCharts.value = false
      await sleep(0.2)
      mountCharts.value = true
    }
  }
)

/** Handle Dark Mode */
const { selectedTheme } = useThemeConfig()
const chartsInDarkMode = ref(selectedTheme.value?.value === 'dark')
watch(
  () => selectedTheme.value,
  (theme) => {
    if (theme?.value === 'dark') {
      return (chartsInDarkMode.value = true)
    }

    chartsInDarkMode.value = false
  }
)

</script>

<template>
  <div v-if="mountCharts" class="mx-auto h-[100%] w-[100%] px-2 md:px-0">
    <!-- Start General Report Cards -->
    <p class="mb-4 mt-2 text-xs font-semibold uppercase text-surface-600 dark:text-surface-400 md:mt-1">General Reports</p>
    <div class="grid grid-cols-1 gap-4 md:grid-cols-2 xl:grid-cols-3">
      <GeneralReportCard
        :id="$.uid + '-people-reached'"
        :categories="peopleReachedCategories"
        :series-data="peopleReachedSeriesData"
        color="#1976d2"
        series-name="People Reached"
        title="Total "
        :total="peopleReachedTotal"
        :dark-mode="chartsInDarkMode"
      />
      <GeneralReportCard
        :id="$.uid + '-expenses'"
        :categories="expensesCategories"
        :series-data="expensesSeriesData"
        color="#CD5C5C"
        series-name="Expenses (PHP)"
        title="Total"
        :total="expensesTotal"
        :dark-mode="chartsInDarkMode"
      />
      <GeneralReportCard
        :id="$.uid + '-onboarded'"
        :categories="onboardedCategories"
        :series-data="onboardedSeriesData"
        color="#088F8F"
        series-name="Onboarded Beneficiaries"
        title="Total"
        :total="onboardedTotal"
        :dark-mode="chartsInDarkMode"
      />
    </div>
    <!-- End General Report Cards -->
   
    
    <!-- End Programs & Timeline -->
  </div>
</template>
