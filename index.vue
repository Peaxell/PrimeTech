<template>
  <div class="auth-landing">
    <div class="left tw-flex tw-flex-col tw-justify-center">
      <h1
        class="tw-text-3xl tw-font-semibold tw-leading-[3rem] tw-text-white tw-ml-[7rem]"
      >
        Prime telecoms <br />
        Admin panel...
      </h1>
      <p class="tw-text-[#6B7280] tw-text-sm tw-mt-4 tw-w-7/12 tw-ml-[7rem]">
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sagittis
        iaculis sit semper nunc tellus dignissim.
      </p>
    </div>
    <div class="right tw-flex tw-flex-col tw-items-center tw-mt-[9rem]">
      <div class="tw-w-full tw-text-center">
        <img
          src="@/assets/svg/prime-telecoms-new.svg"
          alt=""
          class="tw-w-[130px] tw-mx-auto"
        />
        <h2 class="tw-font-semibold tw-text-xl tw-mb-1 tw-mt-7">Welcome</h2>
        <p class="tw-text-brand-primary-1 tw-text-sm">
          Login to access admin features
        </p>

        <v-form ref="form" @submit.prevent="accessAdmin">
          <div class="tw-w-8/12 tw-mx-auto md:tw-w-10/12">
            <div class="tw-flex tw-items-center tw-relative">
              <div
                class="tw-absolute tw-left-0 tw-bottom-[1.45rem] tw-w-full tw-h-[2px] tw-bg-[#ccc]"
              ></div>
              <h3 class="tw-font-bold tw-mr-[4rem] sm:tw-mr-4">Email</h3>
              <v-text-field
                label="Email"
                placeholder="name@company.com"
                type="email"
                v-model="email"
                v-validate="'required|email'"
                data-vv-name="Email"
                required
                hide-details="auto"
                class="mb-6"
              ></v-text-field>
            </div>
            <div class="tw-flex tw-items-center tw-relative">
              <div
                class="tw-absolute tw-left-0 tw-bottom-[1.45rem] tw-w-full tw-h-[2px] tw-bg-[#ccc]"
              ></div>
              <h3 class="tw-font-bold tw-mr-[4rem] sm:tw-mr-4">Password</h3>
              <v-text-field
                label="Password"
                placeholder="*******"
                :type="showPassword ? 'text' : 'password'"
                :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                v-model="password"
                v-validate="{
                  required: true,
                  min: 6,
                }"
                data-vv-name="Password"
                required
                hide-details="auto"
                class="mb-6"
                @click:append="showPassword = !showPassword"
              ></v-text-field>
            </div>

            <v-btn
              depressed
              color="primary"
              type="submit"
              height="50"
              block
              :loading="isLoading"
              class="disabled:tw-bg-slate-300 tw-text-white tw-rounded-xl tw-py-8 tw-mt-6 tw-font-bold tw-transition tw-duration-200 hover:tw-bg-gray-300"
              :disabled="!$helper.fieldsValidated(fields)"
              ><span>Login</span></v-btn
            >
          </div>
        </v-form>
        <p class="tw-text-xs tw-mt-9">© 2022. All rights reserved</p>
      </div>
    </div>
  </div>
</template>

<script>
import PrimeLogo from '~/assets/svg/logo/logo.vue'
import { mapGetters } from 'vuex'

export default {
  layout: 'landing',
  components: {
    PrimeLogo,
  },
  data() {
    return {
      email: null,
      password: null,
      showPassword: false,
    }
  },
  computed: {
    ...mapGetters({
      isLoading: 'request/isLoading',
    }),
  },
  methods: {
    async accessAdmin() {
      const passed = await this.$validator.validateAll()
      if (passed) {
        const data = {
          email: this.email.toLowerCase(),
          password: this.password,
        }
        // await this.$store.dispatch('auth/LOGIN', data)
        try {
          const response = await this.$axios.post('/auth/login', data)
          console.log(response)
        } catch (e) {
          return this.$toaster.error('An error occurred')
        }
      }
    },
  },
}
</script>
<style lang="scss" scoped>
.auth-landing {
  height: 100vh;
  display: flex;
  overflow-y: hidden;

  .left {
    width: 50vw;
    transition: all 0.3s ease-in-out;
    background: url('@/assets/images/admin-login.png') no-repeat center
      center/cover;

    @include breakpoint('sm') {
      display: none;
    }
  }
  .right {
    width: 50vw;
    transition: all 0.3s ease-in-out;
    @include breakpoint('sm') {
      width: 100vw;
    }
  }
}
</style>