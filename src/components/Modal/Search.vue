<template>
  <div>
    <div
      class="modal fade"
      id="searchModal"
      tabindex="-1"
      aria-labelledby="searchModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="searchModalLabel">Pencarian</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="container text-left">
              <div class="row align-items-start">
                <div class="col-md-6">
                  <div class="input-group mb-3">
                    <input
                      type="text"
                      @keypress.enter="initDataTable()"
                      v-model="searchText"
                      class="form-control"
                      placeholder="Ketik nama group, nama pabrik, negara, atau status RSPO"
                      aria-label="Recipient's username"
                      aria-describedby="button-addon2"
                    />
                    <button
                      @click="initDataTable()"
                      class="btn btn-outline-secondary"
                      type="button"
                      id="button-search"
                    >
                      <span class="material-icons"> search </span>
                    </button>
                  </div>
                </div>
                <div class="col-md-12">
                  <table class="table table-hover">
                    <thead>
                      <tr>
                        <th
                          scope="col"
                          v-for="(header, index) in headerlabel"
                          :key="index"
                        >
                          {{ header }}
                        </th>
                      </tr>
                    </thead>
                    <tbody v-if="listData.length > 0">
                      <tr v-for="(data, index) in displayData" :key="index">
                        <th>
                          {{ data["Group Name"] }}
                        </th>
                        <th>
                          {{ data["Mill Name"] }}
                        </th>
                        <th>
                          {{ data["Country"] }}
                        </th>
                        <th>
                          {{
                            data["RSPO Status"] === "None"
                              ? "-"
                              : data["RSPO Status"]
                          }}
                        </th>
                        <th>
                          {{
                            data["RSPO Type"] === "None"
                              ? "-"
                              : data["RSPO Type"]
                          }}
                        </th>
                      </tr>
                    </tbody>
                  </table>
                  <nav
                    aria-label="Page navigation example"
                    style="display: flex; align-items: center"
                  >
                    <ul class="pagination">
                      <li class="page-item">
                        <button
                          type="button"
                          class="page-link"
                          v-if="page != 1"
                          @click="page--"
                        >
                          Previous
                        </button>
                      </li>
                      <li class="page-item">
                        <button
                          type="button"
                          class="page-link"
                          v-for="pageNumber in pages.slice(page - 1, page + 5)"
                          @click="page = pageNumber"
                          :key="pageNumber"
                        >
                          {{ pageNumber }}
                        </button>
                      </li>
                      <li class="page-item">
                        <button
                          type="button"
                          @click="page++"
                          v-if="page < pages.length"
                          class="page-link"
                        >
                          Next
                        </button>
                      </li>
                    </ul>
                    <span type="button" class="page-total">
                      Total pages {{ Math.ceil(listData.length / perPage) }} |
                      Total Data {{ listData.length }}</span
                    >
                  </nav>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Fuse from "fuse.js";

export default {
  name: "search-component",
  props: ["themecolor", "datasets"],
  data() {
    return {
      headerlabel: [
        "Group Name",
        "Mill Name",
        "Country",
        "RSPO Status",
        "RSPO Type",
      ],
      listData: [],
      searchText: "",
      page: 1,
      perPage: 9,
      pages: [],
    };
  },
  computed: {
    displayData() {
      return this.paginate(this.listData);
    },
  },
  watch: {
    listData() {
      this.setPages();
    },
  },
  methods: {
    initDataTable() {
      this.page = 1;
      this.pages = [];
      if (this.searchText === "") {
        this.listData = [];
        this.listData = this.datasets;
      } else {
        this.listData = [];
        const fuseConfig = {
          threshold: 0.2,
          keys: ["Group Name", "Mill Name", "Country", "RSPO Status"],
        };
        const fuse = new Fuse(
          JSON.parse(JSON.stringify(this.datasets)),
          fuseConfig
        );
        const searchedData = fuse.search(this.searchText);
        searchedData.forEach((element) => {
          this.listData.push(element.item);
        });
      }
    },
    setPages() {
      let numberOfPages = Math.ceil(this.listData.length / this.perPage);
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    paginate(datas) {
      let page = this.page;
      let perPage = this.perPage;
      let from = page * perPage - perPage;
      let to = page * perPage;
      return datas.slice(from, to);
    },
  },
  created() {
    this.initDataTable();
  },
};
</script>

<style>
#searchModal {
  filter: v-bind("themecolor");
}
button.page-link {
  display: inline-block;
}
button.page-link {
  font-size: 18px;
  color: #29b3ed;
  font-weight: 500;
}
.offset {
  width: 500px !important;
  margin: 20px auto;
}
#button-search {
  display: flex;
  align-items: center;
}

.page-total {
  margin-left: 15px;
}
.pagination {
  margin: 0;
}
</style>
