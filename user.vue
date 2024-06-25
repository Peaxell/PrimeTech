<template>
  <div>
    <div class="tw-flex tw-items-center tw-pb-2 tw-mt-8">
      <v-icon @click="$router.go(-1)" color="#000">mdi-chevron-left</v-icon>
      <h1 class="tw-font-bold tw-text-2xl sm:tw-text-base">User</h1>
    </div>

    <div class="tw-mt-7">
      <avatar-icon :width="80" :height="80" />

      <div class="tw-w-1/2 sm:tw-w-full">
        <div class="tw-mt-6">
          <h3 class="tw-text-brand-primary tw-text-sm">Details</h3>

          <div class="tw-flex tw-items-center tw-relative tw-text-sm">
            <div
              class="tw-absolute tw-left-0 tw-bottom-[1.45rem] tw-w-full tw-h-[2px] tw-bg-[#ccc]"
            ></div>
            <h3 class="tw-font-bold tw-mr-[4rem] sm:tw-mr-4">Name</h3>
            <v-text-field
              v-model="userName"
              required
              placeholder="Taofeek"
              hide-details="auto"
              class="mb-6 mt-3"
              color="#ccc"
              autofocus
              type="text"
            ></v-text-field>
          </div>
          <div class="tw-flex tw-items-center tw-relative tw-text-sm">
            <div
              class="tw-absolute tw-left-0 tw-bottom-[1.45rem] tw-w-full tw-h-[2px] tw-bg-[#ccc]"
            ></div>
            <h3 class="tw-font-bold tw-mr-[4rem] sm:tw-mr-4">Email</h3>
            <v-text-field
              v-model="email"
              required
              placeholder="user@gmail.com"
              hide-details="auto"
              class="mb-6 mt-3"
              color="#ccc"
              type="email"
            ></v-text-field>
          </div>
          <div class="tw-flex tw-items-center tw-relative tw-text-sm">
            <div
              class="tw-absolute tw-left-0 tw-bottom-[1.45rem] tw-w-full tw-h-[2px] tw-bg-[#ccc]"
            ></div>
            <h3 class="tw-font-bold tw-mr-[4rem] sm:tw-mr-4">Phone</h3>
            <v-text-field
              v-model="phone"
              required
              hide-spin-buttons
              placeholder="+23470123693044"
              hide-details="auto"
              class="mb-6 mt-3"
              color="#ccc"
              type="number"
            ></v-text-field>
          </div>

          <v-btn depressed small block color="primary" height="35"
            ><span class="ml-2">Save Changes</span></v-btn
          >
        </div>

        <div class="tw-mt-10">
          <h3 class="tw-text-brand-primary tw-text-sm">Account Status</h3>
          <h3
            class="tw-bg-red-200 tw-w-max tw-my-2.5 tw-text-xs tw-text-red-600 tw-rounded-3xl tw-px-2 tw-py-1"
          >
            Not verified
          </h3>
          <v-btn depressed small width="140px" color="primary" height="35"
            ><span class="ml-2">Update Status</span></v-btn
          >
        </div>
        <div class="tw-mt-10">
          <h3 class="tw-text-brand-primary tw-text-sm">wallet Balance</h3>
          <h3 class="tw-my-2.5 tw-font-semibold">&#8358; 1000.00</h3>
          <v-btn depressed small width="140px" color="primary" height="35"
            ><span class="ml-2">Update Balance</span></v-btn
          >
        </div>
        <div class="tw-mt-10">
          <h3 class="tw-text-brand-primary tw-text-sm">Date Created</h3>
          <h3 class="tw-mt-2.5 tw-mb-3.5 tw-font-semibold">03 March 2023</h3>
          <v-btn depressed small block color="rgba(204, 8, 8, 0.3)" height="35"
            ><span class="ml-2 tw-text-red-600">Delete User</span></v-btn
          >
        </div>
      </div>

      <div class="tw-mt-12">
        <h3 class="tw-text-brand-primary tw-text-sm tw-mb-6">
          Taofeek's Transaction History
        </h3>

        <v-data-table
          :headers="headers"
          :items="tableItems"
          :items-per-page="5"
        >
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
          <template v-slot:item.receipt="{ item }">
            <v-btn small class="rounded-lg tw-text-xs" color="primary" depressed
              ><v-icon x-small>$receipt</v-icon
              ><span class="tw-ml-1.5">View receipt</span></v-btn
            >
          </template>
        </v-data-table>
      </div>
    </div>
  </div>
</template>

<script>
import AvatarIcon from '~/assets/icons/avatar.vue'

export default {
  layout: 'admin',
  components: {
    AvatarIcon,
  },
  data() {
    return {
      userName: '',
      email: null,
      phone: null,
      headers: [
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
        {
          text: 'Receipt',
          align: 'start',
          sortable: true,
          value: 'receipt',
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
    }
  },
}
</script>
<!-- localhost:3000/admin/customers-->