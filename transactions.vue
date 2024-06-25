<template>
  <div class="tw-mt-12">
    <h1 class="tw-text-xl tw-font-semibold sm:tw-text-lg">Transactions</h1>
    <p class="tw-mt-2 tw-text-brand-primary-1 sm:tw-text-sm">
      The table below shows all transactions made by the users of prime
      telecoms.
    </p>

    <div class="tw-border-b tw-border-brand-primary-1 tw-mt-5">
      <v-tabs
        :show-arrows="true"
        v-model="tab"
        background-color="transparent"
        color="primary"
        center-active
      >
        <v-tab v-for="(item, index) in items" :key="index">
          <!-- {{ item }} -->
          <div class="tw-flex tw-items-center">
            <h1 class="tw-mr-2 tw-normal-case">{{ item.title }}</h1>
            <span
              v-if="item.number"
              class="tw-rounded-full tw-text-xs tw-px-2"
              :class="
                tab === index
                  ? 'tw-text-brand-primary tw-bg-[#E3F4E6]'
                  : 'tw-text-brand-primary-1 tw-bg-[#ccc]'
              "
              >{{ item.length }}</span
            >
          </div>
        </v-tab>
      </v-tabs>
    </div>
    <div class="tw-w-[200px] tw-mt-6">
      <v-text-field
        height="20"
        placeholder="Search"
        v-model="search"
        outlined
        dense
        prepend-inner-icon="mdi-magnify"
      ></v-text-field>
    </div>

    <div class="mt-5">
      <v-data-table :headers="headers" :items="tableItems" :items-per-page="5">
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
        <template v-slot:item.actions="{ item }">
          <span class="tw-flex tw-justify-end">
            <v-icon class="mr-2" @click="confirmDelete(item)"
              >mdi-delete-circle-outline</v-icon
            >
            <v-icon class="mr-2" @click="openEditPage(item)"
              >mdi-pencil-circle-outline</v-icon
            >

            <v-icon class="mr-2 tw-text-red-500" @click="handleDotsClick(item)"
              >mdi-dots-vertical</v-icon
            >
          </span>
        </template>
      </v-data-table>
    </div>

    <!-- Modal for edit page -->
    <v-dialog v-model="showEditPageModal">
      <v-card
        style="
          width: 1175px;
          position: absolute;
          right: 0;
          top: 0;
          padding-bottom: 16rem;
          padding-right: 6%;
          padding-left: 6%;
          padding-top: 3rem;
        "
      >
        <v-card-title class="headline">Customer Account Details</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field
              v-model="selectedCustomer.name"
              label="Name"
              required
            ></v-text-field>
            <v-text-field
              v-model="selectedCustomer.phone"
              label="Phone"
              required
            ></v-text-field>
            <v-text-field
              v-model="selectedCustomer.email"
              label="Email"
              required
            ></v-text-field>
            <v-text-field
              v-model="selectedCustomer.bank"
              label="Bank"
              required
            ></v-text-field>
            <v-text-field
              v-model="selectedCustomer.accountNumber"
              label="Account Number"
              required
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions style="display: flex; justify-content: space-between">
          <v-btn
            style="
              background: #111318;
              color: #fff;
              padding: 1.5rem;
              padding-right: 15%;
              padding-left: 15%;
            "
            @click="showEditPageModal = false"
            >Cancel</v-btn
          >
          <v-btn
            style="
              background: #9bcaa4;
              color: #fff;
              padding: 1.5rem;
              padding-right: 15%;
              padding-left: 15%;
            "
            @click="saveCustomerDetails"
            >Confirm</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Delete Confirmation Modal -->
    <v-dialog v-model="showDeleteDialog" max-width="400px">
      <v-card
        style="padding: 1.3rem 2rem; text-align: center; border-radius: 0.4rem"
      >
        <img
          src="@/assets/images/prime-p-2.png"
          alt=""
          style="text-align: center; height: 3rem; width: 3rem; margin: auto"
        />
        <v-card-title class="headline">
          <v-icon style="color: red; margin-left: 2rem"
            >mdi-delete-circle-outline</v-icon
          >Delete Customer
        </v-card-title>
        <v-card-text
          >Are you sure you want to delete this customer?</v-card-text
        >
        <v-card-actions
          style="
            width: 14rem;
            margin: auto;
            display: flex;
            justify-content: center;
          "
        >
          <v-spacer></v-spacer>
          <v-btn
            style="background: gray; width: 8rem; color: white"
            text
            @click="cancelDelete"
            >Cancel</v-btn
          >
          <v-btn
            style="background: red; width: 8rem; color: white"
            text
            @click="deleteCustomer"
            >Delete</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Confirmation Success Message Modal -->
    <v-dialog v-model="showConfirmSuccessDialog" max-width="400px">
      <v-card
        style="padding: 1.3rem 2rem; text-align: center; border-radius: 0.4rem"
      >
        <v-card-title class="headline">
          <img
            src="@/assets/images/prime-p-2.png"
            alt=""
            style="text-align: center; height: 3rem; width: 3rem; margin: auto"
        /></v-card-title>
        <v-card-text style="font-weight: bolder; color: black"
          >Customer details successfully updated</v-card-text
        >
        <v-card-actions
          style="
            width: 14rem;
            margin: auto;
            display: flex;
            justify-content: center;
          "
        >
          <v-btn
            style="position: absolute; left: 0; top: 0; padding-top: 1rem"
            text
            @click="showConfirmSuccessDialog = false"
            ><v-icon style="color: black; background: transparent"
              >mdi-close-circle</v-icon
            ></v-btn
          >
          <v-icon style="color: #9bcaa4; font-size: 5rem"
            >mdi-check-circle-outline</v-icon
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Success Message Modal -->
    <v-dialog v-model="showSuccessDialog" max-width="400px">
      <v-card
        style="padding: 1.3rem 2rem; text-align: center; border-radius: 0.4rem"
      >
        <v-card-title class="headline">
          <img
            src="@/assets/images/prime-p-2.png"
            alt=""
            style="
              text-align: center;
              height: 3rem;
              width: 3rem;
              margin: auto;
              margin-bottom: 1rem;
            "
        /></v-card-title>
        <v-card-text style="font-weight: bolder; color: black"
          >Customer details successfully deleted</v-card-text
        >
        <v-card-actions
          style="
            width: 14rem;
            margin: auto;
            display: flex;
            justify-content: center;
          "
        >
          <v-icon
            style="position: absolute; left: 0; top: 0; padding-top: 10rem"
            text
            @click="showSuccessDialog = false"
          >
            mdi-close-circle
          </v-icon>
          <v-icon style="color: #9bcaa4; font-size: 5rem"
            >mdi-check-circle-outline</v-icon
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
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
      showDeleteDialog: false,
      showSuccessDialog: false,
      showEditPageModal: false,
      showConfirmSuccessDialog: false,
      selectedCustomer: {},
      tab: 0,
      search: '',
      headers: [
        { text: 'Name', value: 'customer' },
        { text: 'Transaction ID', value: 'transactionID' },
        { text: 'Amount', value: 'amount' },
        { text: 'Date', value: 'date' },
        { text: 'Status', value: 'status' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      items: [
        {
          title: 'All',
          number: true,
          length: 6,
        },
        {
          title: 'Data',
          number: true,
          length: 6,
        },
        {
          title: 'Airtime',
          number: true,
          length: 6,
        },
        {
          title: 'Funding',
          number: true,
          length: 6,
        },
        {
          title: 'Electricity bill payment',
          number: true,
          length: 6,
        },
        {
          title: 'Cable subscription',
          number: true,
          length: 6,
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
  methods: {
    confirmDelete(item) {
      this.selectedCustomer = item
      this.showDeleteDialog = true
    },
    deleteCustomer() {
      // Logic to delete the customer
      this.showDeleteDialog = false
      this.showSuccessDialog = true
    },
    cancelDelete() {
      this.showDeleteDialog = false
    },
    openEditPage(item) {
      this.selectedCustomer = item
      this.showEditPageModal = true
    },
    saveCustomerDetails() {
      // Logic to save customer details
      this.showEditPageModal = false
      this.showConfirmSuccessDialog = true
    },
    handleDotsClick(item) {
      // Logic to handle dots click
      console.log('Dots clicked for item:', item)
    },
  },
}
</script>

<style scoped>
/* Add any scoped styles here */
</style>
