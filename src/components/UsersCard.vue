<template>
  <div class="p-2">
    <div
      v-if="isLoading"
      style="height: calc(95vh)"
      class="d-flex justify-content-center align-items-center"
    >
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <div v-else class="d-flex flex-wrap justify-content-center">
      <div
        :key="user.email"
        v-for="user in usersToBeShown"
        class="card card-user m-3"
      >
        <img :src="user.picture" class="card-img-top" alt="..." />
        <div class="card-body">
          <h5 class="card-title">{{ user.name }}</h5>
          <p class="card-text mb-0">Ph: {{ user.phone }}</p>
          <p class="card-text mb-0">Email: {{ user.email }}</p>
          <p class="card-text">Country: {{ user.country }}</p>
        </div>
      </div>
      <div class="d-flex w-100 flex-wrap justify-content-center">
        <page-selector
          :page="page"
          :totalPages="totalPages"
          @changePage="changePage"
        />
        <div class="d-flex flex-shrink-1 justify-content-center">
          <p class="d-flex justify-content-end align-items-center mb-0 me-2">
            Items per page:
          </p>
          <div class="d-block" style="margin: auto 0">
            <select
              @change="changeItemsPerPage($event)"
              class="form-select form-select-sm"
              style="width: 5rem"
            >
              <option selected value="5">5</option>
              <option value="10">10</option>
              <option value="15">15</option>
              <option value="20">20</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import PageSelector from "./PageSelector.vue";

export default {
  components: { PageSelector },
  data: () => ({
    isLoading: true,
    users: [],
    usersToBeShown: [],
    page: 1,
    itemsToBeShown: 5,
    totalPages: 5,
  }),
  async mounted() {
    await this.getUsers();
    this.usersToBeShown = this.users.filter((e, i) => {
      return i < this.itemsToBeShown;
    });
    this.totalPages = Math.ceil(this.users.length / this.totalPages);
  },
  watch: {
    page: function (newValue) {
      const index = newValue * this.itemsToBeShown - this.itemsToBeShown;
      this.usersToBeShown = this.users.filter((e, i) => {
        return i >= index && i < index + this.itemsToBeShown;
      });
    },
    itemsToBeShown: function (newValue) {
      const index = this.page * newValue - newValue;
      this.usersToBeShown = this.users.filter((e, i) => {
        return i >= index && i < index + newValue;
      });
    },
  },
  methods: {
    async getUsers() {
      this.isLoading = true;

      let fetchUrl = "https://randomuser.me/api/?";
      const seed = window.localStorage.getItem("seed");
      if (!!seed) fetchUrl += `seed=${seed}&`;
      const response = await fetch(`${fetchUrl}results=20`);
      const { info, results } = await response.json();

      if (!seed) window.localStorage.setItem("seed", info.seed);

      this.users = results.map((e) => ({
        name: e.name.first + " " + e.name.last,
        email: e.email,
        picture: e.picture.large,
        phone: e.phone,
        country: e.location.country,
      }));

      this.isLoading = false;
    },
    changePage(p) {
      this.page = p;
    },
    changeItemsPerPage(event) {
      const val = parseInt(event.target.value);
      this.itemsToBeShown = val;
      this.totalPages = Math.ceil(this.users.length / val);
      if (this.page > this.totalPages) this.page = this.totalPages;
    },
  },
};
</script>

<style>
.card-user {
  flex: 0 1 18rem;
}
</style>