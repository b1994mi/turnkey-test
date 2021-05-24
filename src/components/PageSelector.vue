<template>
  <div class="d-flex justify-content-center flex-grow-1">
    <ul class="pagination my-3">
      <li :class="['page-item', { disabled: page <= 1 }]">
        <a class="page-link" href="#" @click="changePage(page - 1)">Previous</a>
      </li>
      <li
        :key="pageNumber"
        v-for="pageNumber in totalPages"
        :class="['page-item', { active: pageNumber == page }]"
      >
        <a class="page-link" href="#" @click="changePage(pageNumber)">
          {{ pageNumber }}
        </a>
      </li>
      <li :class="['page-item', { disabled: page >= totalPages }]">
        <a class="page-link" href="#" @click="changePage(page + 1)">Next</a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "PageSelector",
  props: ["page", "totalPages"],
  methods: {
    changePage(pageNumber) {
      this.$emit("changePage", pageNumber);
    },
  },
};
</script>
