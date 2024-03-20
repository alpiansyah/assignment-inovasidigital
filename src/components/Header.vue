<template>
  <div id="header-nav">
    <nav class="navbar navbar-expand-lg navbar-light">
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarTogglerDemo01"
        aria-controls="navbarTogglerDemo01"
        aria-expanded="true"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarTogglerDemo01">
        <a class="navbar-brand" href="#">Inovasi Digital</a>
        <ul class="navbar-nav mr-auto mt-2 mt-lg-0">
          <li class="nav-item">
            <a class="nav-link" href="#" @click="filterModal()">
              <div class="nav-a">
                <span class="material-icons"> filter_alt </span>
                Filter Map
              </div>
            </a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#" @click="statisticModal()">
              <div class="nav-a">
                <span class="material-icons"> show_chart </span>
                Statistik
              </div>
            </a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#" @click="searchModal()">
              <div class="nav-a">
                <span class="material-icons"> search </span>
                Cari
              </div></a
            >
          </li>
        </ul>
      </div>
    </nav>
  </div>
</template>
<script>
export default {
  name: "header-component",
  props: ["themecolor"],
  methods: {
    //berikut adalah script untuk memunculkan popup
    filterModal() {
      const filterModal = new bootstrap.Modal("#filterModal", {
        keyboard: false,
      });
      filterModal.toggle();
    },
    statisticModal() {
      const statisticModal = new bootstrap.Modal("#statisticModal", {
        keyboard: false,
      });
      statisticModal.toggle();
    },
    searchModal() {
        const searchModal = new bootstrap.Modal("#searchModal", {
        keyboard: false,
      });
      searchModal.toggle();
    },
  },
};
</script>

<style>
#header-nav {
  padding-left: 15px;
  filter: v-bind("themecolor");
  background: beige;
}

.navbar {
  padding-top: 10px !important;
}

.nav-a {
  display: flex;
}
</style>
