<template>
  <h1 v-if="loading">Loading...</h1>
  <h1 class="title" v-else>Item shop</h1>
  <div class="content">
    <div class="button__wrapper">
      <input
        class="input input--search"
        type="text"
        v-model="search"
        placeholder="Search.."
        @input="filteredList"
      />
      <button class="button" @click="filterHandler(All)">All</button>
      <button
        class="button"
        v-for="buttonName in duplicate"
        :key="buttonName"
        @click="filterHandler(buttonName)"
      >
        {{ buttonName.charAt(0).toUpperCase() + buttonName.slice(1) }}
      </button>
    </div>
    <div class="list__items">
      <div
        class="item"
        :title="item.description"
        v-for="item in filterByCategory"
        :key="item.id"
      >
        <div class="image__wrapper">
          <img class="image" :src="item.image" alt="image" />
        </div>
        <p class="item__title">{{ item.title }}</p>
        <p class="item__rating">Rating: {{ item.rating.rate }}</p>
        <p class="item__count">Count: {{ item.rating.count }}</p>
        <p class="item__price">
          {{ item.price }} €
          <span class="item__sale">{{ Math.floor(+item.price + 50) }} €</span>
        </p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { mapGetters } from "vuex";

export default defineComponent({
  name: "App",
  data: () => ({
    filterByCategory: [],
    category: [],
    search: "",
    loading: true,
  }),

  methods: {
    filterHandler(buttonName: string, buttonAll: string) {
      if (buttonName === buttonAll) {
        this.onLoad();
      } else {
        const category = this.data.filter((item: { category: string }) => {
          return item.category === buttonName;
        });
        this.category = category;
        this.filterByCategory = category;
        this.search = "";
      }
    },
    onLoad() {
      this.filterByCategory = this.data;
      this.category = this.data;
    },

    filteredList() {
      const searchItem = this.filterByCategory.filter(
        (item: { title: string }) => {
          return item.title.toLowerCase().includes(this.search.toLowerCase());
        }
      );
      this.search.trim()
        ? (this.filterByCategory = searchItem)
        : (this.filterByCategory = this.category);
    },
  },

  computed: {
    ...mapGetters({ data: "allItems" }),
    duplicate() {
      const buttonName = this.data.map(
        (item: { category: string }) => item.category
      );
      const categoryButtons = [...new Set(buttonName)].sort();
      return categoryButtons;
    },
  },

  async created() {
    await this.$store.dispatch("getItems");
    this.onLoad();
    this.loading = false;
  },
});
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.title {
  margin-bottom: 60px;
}

.input {
  padding: 8px 15px;
  width: 300px;
  border-radius: 4px;
  border: 2px solid #9eb1aa;
  &:focus {
    box-shadow: 0 0 0 0.15rem #2c3e50;
    border: 2px solid transparent;
  }
}

.content {
  margin: 0 auto;
  max-width: 1400px;
  width: 100%;
}

.button__wrapper {
  margin: 0 auto;
  padding-bottom: 60px;
  width: 870px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.button {
  border: none;
  padding: 10px 15px;
  cursor: pointer;
  font-weight: 500;
  background-color: #9eb1aa;
  border-radius: 4px;
  color: #ffffff;
  font-size: 14px;
  &:hover {
    box-shadow: 0 0 0 0.1rem #2c3e50;
  }
}

.list__items {
  display: grid;
  justify-content: center;
  grid-template-columns: repeat(auto-fit, 350px);
  gap: 30px;
}

.item {
  margin-bottom: 20px;
  border-radius: 8px;
  border: 2px solid #9eb1aa;
  padding: 20px 10px;
  cursor: pointer;
  transition: all ease 0.3s;
  position: relative;
}

.tooltip {
  position: absolute;
  top: 0;
  left: 0;
  font-size: 20px;
  display: none;
  &.active {
    display: block;
  }
}

.item:hover {
  box-shadow: 0 1px 10px #292828;
}
.item__title {
  font-weight: 700;
  margin-bottom: 15px;
  font-size: 18px;
}

.item__rating,
.item__count {
  font-weight: 600;
  line-height: 1.4;
  margin: 0;
  padding: 0;
}

.item__price {
  color: #e44d39;
  font-size: 24px;
  font-weight: 700;
}

.item__sale {
  color: #292828;
  text-decoration: line-through;
  font-size: 16px;
}

.image {
  object-fit: contain;
  width: 300px;
  height: 350px;
}

.image__wrapper {
  margin: 0 auto;
  width: 300px;
  height: 350px;
  overflow: hidden;
}
</style>
