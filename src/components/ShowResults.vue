<template>
  <div>
    <button @click="removeSelectedLocs" :disabled="selectedLocations.length === 0">
      Remove Selected
    </button>
    <table>

      <thead>
        <tr>
          <th>Select</th>
          <th>Coordinates</th>
          <th>Name</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="(item) in currentPageContent" :key="item.coords.lat + ',' + item.coords.lng">
          <td>
            <input type="checkbox" v-model="selectedLocations" :value="item.coords.lat()+ ',' + item.coords.lng()" />
          </td>
          <td>{{ item.coords.lat() }}, {{ item.coords.lng() }}</td>
          <td>{{ item.name }}</td>
        </tr>
      </tbody>

    </table>

    <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span id="between"> Page {{ currentPage }} of {{ totalPages }} </span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>

  </div>
</template>

<script>
export default {
  props: {allResults : Object},
  watch: {
    allResults: function () {
        this.locations = this.allResults.results
    }
  },
  data() {
    return {
      locations: [],
      selectedLocations: [], 
      currentPage: 1,
      locsPerPage: 10,
    };
  },
  computed: {
    currentPageContent() {
      const start = (this.currentPage - 1) * this.locsPerPage
      const end = start + this.locsPerPage
      return this.locations.slice(start, end)
    },
    totalPages() {
        return Math.ceil(this.locations.length / this.locsPerPage)
    },
  },
  methods: {
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++
      }
    },
    removeSelectedLocs() {
      this.locations = this.locations.filter((item) => !this.selectedLocations.includes(item.coords.lat()+ ',' + item.coords.lng()));
      this.selectedLocations = []
      this.$emit('LocationDeleted', this.locations)
    },
  },
};
</script>

<style>
.pagination {
  text-align: center;
  margin-top: 20px;
}
table {
  border-collapse: collapse;
  width: 80%;
  margin: 0 auto;
}
th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}
th {
  background-color: #f2f2f2;
}
#between {
    margin-right: 5px;
    font-family: 'Helvetica Neue', sans-serif;
    color: #0faf87;
}
</style>
