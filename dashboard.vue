<template>
  <div class="tw-mt-12">
    <h1 class="tw-text-xl tw-font-semibold sm:tw-text-lg">Welcome back 👋</h1>
    <p class="tw-mt-2 tw-text-brand-primary-1 sm:tw-text-sm">
      Lorem ipsum dolor sit amet consectetur. Ultricies hac mauris commodo
      ligula egestas penatibus turpis ipsum maecenas.
    </p>
    <div class="tw-grid tw-grid-cols-2 tw-gap-x-3 tw-my-6 sm:tw-grid-cols-1">
      <div
        class="box tw-flex tw-justify-between tw-items-center tw-px-5 tw-py-5 sm:tw-mb-3"
      >
        <div>
          <h3 class="tw-text-sm tw-text-brand-primary tw-mb-0.5">
            Total users
          </h3>
          <h2 class="tw-font-semibold tw-text-xl sm:tw-text-lg">5,000</h2>
        </div>
        <dashboard-user />
      </div>
      <div
        class="box tw-flex tw-justify-between tw-items-center tw-px-5 tw-py-5"
      >
        <div>
          <h3 class="tw-text-sm tw-text-brand-primary tw-mb-0.5">Purchases</h3>
          <h2 class="tw-font-semibold tw-text-xl sm:tw-text-lg">₦580,000.00</h2>
        </div>
        <dashboard-naira />
      </div>
    </div>

    <div class="tw-grid tw-grid-cols-2 tw-gap-x-5 tw-mb-5 md:tw-grid-cols-1">
      <div class="left box tw-p-5 sm:tw-mb-4">
        <div class="tw-mb-5 tw-flex tw-justify-between tw-items-center">
          <div>
            <h5 class="tw-text-sm tw-text-brand-primary">Statistics</h5>
            <h3 class="tw-font-semibold">Sales activities</h3>
          </div>
          <div class="tw-w-[120px]">
            <v-select
              :items="['2023', '2022', '2021']"
              outlined
              rounded
              background-color="#F8F8FF"
              value="2023"
              dense
              class="mt-4 text-subtitle-2"
              append-icon="mdi-chevron-down"
              color="#615E83"
            ></v-select>
          </div>
        </div>
        <apexchart
          type="pie"
          width="380"
          :options="chartOptions"
          :series="pieSeries"
        ></apexchart>
      </div>
      <div class="right">
        <div class="tw-mb-5 tw-flex tw-justify-between tw-items-center">
          <h3 class="tw-font-semibold">User Activity</h3>
          <div class="tw-w-[120px] tw-text-sm">
            <v-select
              :items="['Weekly', 'Monthly', 'Yearly']"
              outlined
              value="Weekly"
              dense
              class="mt-4 text-subtitle-2"
              append-icon="mdi-chevron-down"
              color="#E71212"
            ></v-select>
          </div>
        </div>
        <apexchart
          width="450"
          type="bar"
          :options="options"
          :series="series"
        ></apexchart>
      </div>
    </div>

    <!-- User Activity Table -->
    <div class="tw-mt-8">
      <h2 class="tw-font-semibold tw-mb-4">Recent User Activity</h2>
      <div>
        <v-data-table
          :headers="headers"
          :items="tableItems"
          :items-per-page="5"
        >
          <template v-slot:header.name="{ header }">
            <span class="tw-bg-brand-primary-1">
              {{ header.text.toUpperCase() }}
            </span>
          </template>
          <template v-slot:item.customer="{ item }">
            <span class="tw-flex tw-items-center">
              <avatar-icon :width="30" :height="30" />
              <span class="tw-font-medium tw-ml-2">{{ item?.name }}</span>
            </span>
          </template>
          <template v-slot:item.transactionID="{ item }">
            <span>
              {{ item?.transactionID }}
            </span>
          </template>
          <template v-slot:item.status="{ item }">
            <span
              v-if="item?.status == 'Successful' || item?.status == 'delivered'"
              class="tw-bg-green-200 tw-text-xs tw-text-green-600 tw-rounded-3xl tw-px-2 tw-py-1"
              >{{ item?.status }}</span
            >
            <span
              v-if="item?.status == 'PENDING'"
              class="tw-bg-yellow-200 tw-text-xs tw-text-yellow-600 tw-rounded-3xl tw-px-2 tw-py-1"
              >Pending</span
            >
            <span
              v-if="item?.status == 'Failed'"
              class="tw-bg-red-200 tw-text-xs tw-text-red-600 tw-rounded-3xl tw-px-2 tw-py-1"
              >{{ item?.status }}</span
            >
          </template>
        </v-data-table>
      </div>
    </div>
  </div>
</template>
<script>
import DashboardUser from '@/assets/icons/admin/dashboard-user.vue'
import DashboardNaira from '@/assets/icons/admin/dashboard-naira.vue'
import AvatarIcon from '~/assets/icons/avatar.vue'

export default {
  layout: 'admin',
  components: {
    DashboardUser,
    DashboardNaira,
    AvatarIcon,
  },
  data() {
    return {
      headers: [
        {
          text: 'Customer',
          align: 'start',
          sortable: true,
          value: 'customer',
        },
        {
          text: 'Transaction ID',
          align: 'start',
          sortable: true,
          value: 'transactionID',
        },
        {
          text: 'Type',
          align: 'start',
          sortable: true,
          value: 'type',
        },
        {
          text: 'Amount',
          align: 'start',
          sortable: true,
          value: 'amount',
        },
        {
          text: 'Date',
          align: 'start',
          sortable: true,
          value: 'date',
        },
        {
          text: 'Status',
          align: 'start',
          sortable: true,
          value: 'status',
        },
      ],
      tableItems: [
        {
          name: 'Taofeek',
          transactionID: 'MNFY|27|20230303193905|144281',
          type: 'Data',
          amount: '₦1000.0',
          date: '03 March 2023',
          status: 'Successful',
        },
        {
          name: 'Mairo',
          transactionID: 'MNFY|27|20230303193905|144281',
          type: 'Airtime',
          amount: '₦1000.0',
          date: '03 March 2023',
          status: 'Failed',
        },
        {
          name: 'Joshua',
          transactionID: 'MNFY|27|20230303193905|144281',
          type: 'Electricity',
          amount: '₦1000.0',
          date: '03 March 2023',
          status: 'Successful',
        },
        {
          name: 'Reighneth',
          transactionID: 'MNFY|27|20230303193905|144281',
          type: 'Airtime',
          amount: '₦1000.0',
          date: '03 March 2023',
          status: 'Successful',
        },
        {
          name: 'Japheth',
          transactionID: 'MNFY|27|20230303193905|144281',
          type: 'Data',
          amount: '₦1000.0',
          date: '03 March 2023',
          status: 'Successful',
        },
      ],
      options: {
        plotOptions: {
          bar: {
            borderRadius: 10,
            borderRadiusApplication: 'end',
          },
        },
        chart: {
          id: 'vuechart-example',
          toolbar: {
            show: false,
          },
        },
        xaxis: {
          categories: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        },
        yaxis: {
          labels: {
            formatter: function (value) {
              return value + '%'
            },
          },
        },
        colors: ['#9BCAA4'],
      },
      series: [
        {
          name: 'user-activity',
          data: ['76%', '100%', '60%', '58%', '40%', '20%', '27%'],
        },
      ],

      pieSeries: [44, 55, 13, 43],
      chartOptions: {
        chart: {
          width: 280,
          type: 'pie',
        },
        labels: ['Airtime', 'Data', 'Cable Tv', 'Electricity'],
        responsive: [
          {
            breakpoint: 480,
            options: {
              chart: {
                width: 200,
              },
              legend: {
                position: 'bottom',
              },
            },
          },
        ],
        colors: ['#9BCAA4', '#ccc', '#E3F4E6', '#C3DFC8'],
      },
    }
  },

  methods: {
    async getStats() {
      try {
        const response = await this.$axios.get('/admin/getUserDetails')
        consol.log(response)
      } catch (e) {
        return this.$toaster.error('An error occurred')
      }
    },
  },
  async mounted() {
    // await this.getStats()
    console.log(this.$route.path)
    console.log(this.$route)
  },
}
</script>
<style lang="scss" scoped>
.box {
  border-radius: 20px;
  background: #fff;
  box-shadow: 0 2px 6px 0 rgba(13, 10, 44, 0.15);
}
</style>