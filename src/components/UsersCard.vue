<template>
  <div>
    <div v-if="isLoading" class="d-flex justify-content-center">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <div v-else>
      <div
        v-for="user in users"
        :key="user.email"
        class="card"
        style="width: 18rem"
      >
        <img :src="user.picture" class="card-img-top" alt="..." />
        <div class="card-body">
          <h5 class="card-title">{{ user.name }}</h5>
          <p class="card-text mb-0">Ph: {{ user.phone }}</p>
          <p class="card-text mb-0">Email: {{ user.email }}</p>
          <p class="card-text">Country: {{ user.country }}</p>
          <a href="#" class="btn btn-primary">Go somewhere</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    isLoading: true,
    users: [],
  }),
  mounted() {
    this.getUsers();
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
  },
};
</script>
