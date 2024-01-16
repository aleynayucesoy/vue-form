<template>
  <div class="container px-40 bg-white shadow-lg">
    <form v-if="step !== 2" @submit.prevent="submitNewUser">
      <div id="step1" class="my-4 flex flex-col">
        <h1>Step 1</h1>

        <button
          type="button"
          @click="generateToken"
          class="mt-4 mb-2 bg-cyan-500 hover:bg-cyan-600 border-none py-4 text-white"
        >
          Generate Token
        </button>
        <input v-model="form.token" data-readonly required />
      </div>

      <div id="step2" class="mb-4 flex flex-col">
        <h1>Step 2</h1>

        <p>X-Auth Header Secret Key: <input readonly v-model="form.xauth" /></p>
        <p>Unix Timestamp: <input v-model="form.unix" required /></p>
        <p>Before Base64: <input v-model="form.bBase64" required /></p>
        <p>After Base64: <input v-model="form.aBase64" required /></p>
      </div>

      <div id="step3" class="mb-4 flex flex-col">
        <h1>Step 3</h1>

        <button
          type="button"
          @click="fillNewUserForm"
          class="mt-4 mb-2 bg-cyan-500 hover:bg-cyan-600 border-none py-4 text-white"
        >
          Fill New User Form
        </button>

        <div class="flex pb-2">
          <label for="name" class="flex-initial w-20">Name:</label>
          <input type="text" v-model="form.name" id="name" required />
        </div>
        <div class="flex pb-2">
          <label for="surname" class="flex-initial w-20">Surname:</label>
          <input type="text" v-model="form.surname" id="surname" required />
        </div>
        <div class="flex pb-2">
          <label for="mail" class="flex-initial w-20">Mail:</label>
          <input type="email" v-model="form.mail" id="mail" required />
        </div>
        <div class="flex pb-2">
          <label for="password" class="flex-initial w-20">Password:</label>
          <input
            type="password"
            v-model="form.password"
            id="password"
            required
          />
        </div>
        <div class="flex">
          <label for="packageId" class="flex-initial w-20">Package Id:</label>
          <select v-model="form.packageId" id="packageId">
            <option v-for="option in form.options" :value="option.value">
              {{ option.text }}
            </option>
          </select>
        </div>
        <button
          type="submit"
          class="my-4 bg-cyan-500 hover:bg-cyan-600 border-none py-4 text-white"
        >
          Submit New User
        </button>
      </div>
    </form>

    <div v-if="step === 2" class="py-10">
      <loading
        v-model:active="isLoading"
        :can-cancel="true"
        :on-cancel="onCancel"
        :is-full-page="fullPage"
      />

      <h1>Result Page</h1>
      <p>Main URL: {{ result.mainUrl }}</p>
      <p>Token Path: {{ result.tokenPath }}</p>
      <p>New User Path: {{ result.newUserPath }}</p>
    </div>
  </div>
</template>

<script>
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/css/index.css";

export default {
  data() {
    return {
      step: 1,
      isLoading: false,
      form: {
        token: "",
        xauth: "5ae8e28d805009c1ddc88245a7dbef74",
        unix: Math.round(+new Date() / 1000),
        bBase64: "",
        aBase64: "",
        name: "John",
        surname: "",
        mail: "",
        password: "",
        packageId: "A",
        options: [
          { text: "GOLD", value: "A" },
          { text: "DIAMOND (Discontinued)", value: "B" },
          { text: "SILVER", value: "C" },
          { text: "PREMIUM (Discontinued)", value: "D" },
        ],
        result: {
          mainUrl: "",
          tokenPath: "",
          newUserPath: "",
        },
      },
    };
  },
  components: {
    Loading,
  },
  methods: {
    generateRandomToken(length) {
      const characters =
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
      const charactersLength = characters.length;
      let token = "";

      for (let i = 0; i < length; i++) {
        const randomIndex = Math.floor(Math.random() * charactersLength);
        token += characters.charAt(randomIndex);
      }
      return token;
    },
    generateToken() {
      const randomToken = this.generateRandomToken(10);
      this.form.token = randomToken;
      this.form.bBase64 = btoa(this.form.token);
      this.form.aBase64 = btoa(this.form.bBase64);
    },
    fillNewUserForm() {
      this.form.name = "";
      this.form.surname = "";
      this.form.mail = "";
      this.form.password = "";
      this.form.packageId = "A";
    },
    async submitNewUser() {
      if (this.step == 1) {
        this.isLoading = true;
        this.result = {
          mainUrl: "",
          tokenPath: "",
          newUserPath: "",
        };

        setTimeout(() => {
          this.result = {
            mainUrl: "https://api-test2.ubuntutradebot.com",
            tokenPath: "/api/auth/generate-token/en",
            newUserPath: "/api/auth/new-user/en",
          };
          this.isLoading = false;
        }, 1000);
      }

      this.step = 2;
      this.$emit("submitNewUser", this.form);
    },
  },
};
</script>

<style scoped></style>
