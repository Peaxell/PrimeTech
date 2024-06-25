<template>
  <div class="tw-mt-12">
    <h1 class="tw-text-xl tw-font-semibold sm:tw-text-lg">Funds</h1>
    <p class="tw-mt-2 tw-text-brand-primary-1 sm:tw-text-sm">
      All users funding of account.
    </p>

    <div class="tw-flex tw-justify-between tw-items-center tw-mt-5">
      <div class="tw-w-[200px]">
        <v-text-field
          height="20"
          placeholder="Search"
          v-model="search"
          outlined
          dense
          prepend-inner-icon="mdi-magnify"
        ></v-text-field>
      </div>

      <v-btn depressed small color="primary">
        <v-icon>mdi-plus</v-icon><span class="ml-2">Fund User</span>
      </v-btn>
    </div>
    <div class="mt-5">
      <v-data-table :headers="headers" :items="tableItems" :items-per-page="5">
        <template v-slot:header.name="{ header }">
          <span class="tw-bg-brand-primary-1">
            {{ header.text.toUpperCase() }}
          </span>
        </template>
        <template v-slot:item.customer="{ item }">
          <nuxt-link to="/admin/customers/user">
            <span class="tw-flex tw-items-center tw-text-black">
              <avatar-icon :width="30" :height="30" />
              <span class="tw-font-medium tw-ml-2">{{ item?.name }}</span>
            </span>
          </nuxt-link>
        </template>
        <template v-slot:item.transactionID="{ item }">
          <nuxt-link to="/admin/customers/user">
            <span class="tw-text-black">
              {{ item?.transactionID }}
            </span>
          </nuxt-link>
        </template>
        <template v-slot:item.type="{ item }">
          <nuxt-link to="/admin/customers/user">
            <span class="tw-text-black">
              {{ item?.type }}
            </span>
          </nuxt-link>
        </template>
        <template v-slot:item.amount="{ item }">
          <nuxt-link to="/admin/customers/user">
            <span class="tw-text-black">
              {{ item?.amount }}
            </span>
          </nuxt-link>
        </template>
        <template v-slot:item.date="{ item }">
          <nuxt-link to="/admin/customers/user">
            <span class="tw-text-black">
              {{ item?.date }}
            </span>
          </nuxt-link>
        </template>
        <template v-slot:item.status="{ item }">
          <nuxt-link to="/admin/customers/user">
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
          </nuxt-link>
        </template>
        <!--icons template-->
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
          width: 1220px;
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
              margin-bottom: 1.9rem;
            "
        /></v-card-title>
        <v-card-text style="font-weight: 700; color: black"
          >Customer successfully deleted</v-card-text
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
            @click="showSuccessDialog = false"
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
      search: '',
      showDeleteDialog: false,
      showSuccessDialog: false,
      showEditPageModal: false,
      showConfirmSuccessDialog: false,
      deleteItem: null,
      selectedCustomer: {
        name: '',
        phone: '',
        email: '',
        bank: '',
        accountNumber: '',
      },
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
        {
          text: 'Actions',
          align: 'end',
          sortable: false,
          value: 'actions',
        },
      ],
      tableItems: [
        {
          name: 'Diane Cooper',
          transactionID: 'ID-11238',
          type: 'User',
          amount: '3,000.00',
          date: 'Mar 12, 2023',
          status: 'Successful',
        },
        {
          name: 'Jhone Doe',
          transactionID: 'ID-11238',
          type: 'Agent',
          amount: '3,000.00',
          date: 'Mar 12, 2023',
          status: 'Failed',
        },
        {
          name: 'Alice Chris',
          transactionID: 'ID-11238',
          type: 'Agent',
          amount: '3,000.00',
          date: 'Mar 12, 2023',
          status: 'delivered',
        },
        {
          name: 'John Doe',
          transactionID: 'ID-11238',
          type: 'User',
          amount: '3,000.00',
          date: 'Mar 12, 2023',
          status: 'PENDING',
        },
      ],
    }
  },
  methods: {
    openEditPage(item) {
      this.selectedCustomer = { ...item }
      this.showEditPageModal = true
    },

    saveCustomerDetails() {
      // Save the customer details logic here
      this.showEditPageModal = false
      this.showConfirmSuccessDialog = true
    },
    confirmDelete(item) {
      this.deleteItem = item
      this.showDeleteDialog = true
    },
    cancelDelete() {
      this.showDeleteDialog = false
      this.deleteItem = null
    },
    deleteCustomer() {
      this.tableItems = this.tableItems.filter(
        (item) => item !== this.deleteItem
      )
      this.showDeleteDialog = false
      this.showSuccessDialog = true
    },
  },
}
</script>

<style scoped>
.back {
  background-color: transparent;
  color: black;
}
</style>
