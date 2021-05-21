<template>
  <div class="products">
    <h1>Список товаров</h1>
    <div class="filters">
      <form>
        <div v-show="showFilters" class="filters__inputs">
          <input type="text" placeholder="Артикул" v-model="filters.searchedCode"/>
          <input type="list" placeholder="Название" v-model="filters.searchedName"/>
          <input type="list" placeholder="Производитель" v-model="filters.searchedManufacturer"/>
          <input type="list" placeholder="Макс. цена" v-model="filters.searchedPrice"/>
          <input type="list" placeholder="Мин. шт. на складе" v-model="filters.searchedAvailability"/>
        </div>
      </form>
      <button @click="showFilters = !showFilters">Фильтры</button>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th>Артикул</th>
          <th>Название</th>
          <th>Производитель</th>
          <th>Цена</th>
          <th>Наличие на складе</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="product in displayedItems"
          :key="product.code"
        >
          <td>{{product.code}}</td>
          <td>{{product.title}}</td>
          <td>{{product.manufacturer}}</td>
          <td>{{product.price}}</td>
          <td>{{product.stockAmount}}</td>
        </tr>
      </tbody>   
    </table>
    <div class="navigation"> 
          <div class="table__reset_filter">
            <button @click="resetFilters">Сбросить фильтры</button>
          </div>
          <div class="table__pagination">
            <button class="page"
              v-for="page in pages"
              :key="page"
              :class="{'active_page': page === currentPage}"
              @click="changePage(page)"
            >
              {{page}}
            </button>
          </div> 
          <div>
            <p>Выбрано товаров: {{filteredProducts.length}}</p>
          </div> 
        </div>
        <div>
            <p>Всего товаров: {{items.length}}</p>
        </div>
  </div>
</template>

<script>
import products from '../products.json'

export default {
  name: 'Products',
  data() {
    return {
      items: products,
      productsOnPage: 5,
      currentPage: 1,
      showFilters: false,
      filters: {
        searchedCode: '',
        searchedName: '',
        searchedManufacturer: '',
        searchedPrice: '',  
        searchedAvailability: ''
      },
      filteredProducts: []
    }
  },
  methods: {
    paginate(list) {
      let from = (this.currentPage * this.productsOnPage) - this.productsOnPage;
      let to = (this.currentPage * this.productsOnPage);
      return list.slice(from, to);
    },
    changePage(page) {
      this.currentPage = page;
    },
    filterProducts(list) {
      let code = this.filters.searchedCode;
      let name = this.filters.searchedName;
      let manufacturer = this.filters.searchedManufacturer;
      let price = this.filters.searchedPrice;
      let stock = this.filters.searchedAvailability;
      if (code || name || manufacturer || stock || price) {
        let filtered = list.filter(function (element) {
          if (price.length > 0) {
            return element.code.includes(code) && element.title.toLowerCase().includes(name) && element.manufacturer.toLowerCase().includes(manufacturer) && element.stockAmount >= Number(stock) && element.price <= Number(price);
          }
          return element.code.includes(code) && element.title.toLowerCase().includes(name) && element.manufacturer.toLowerCase().includes(manufacturer) && element.stockAmount >= Number(stock);
        })
        return this.filteredProducts = filtered;
      }
      return this.filteredProducts = list;
    },
    resetFilters() {
      this.filteredProducts = this.items;
      this.filters.searchedCode = '';
      this.filters.searchedName = '';
      this.filters.searchedManufacturer = '';
      this.filters.searchedPrice = '';
      this.filters.searchedAvailability = '';
    }
  },
  computed: {
    pages() {
      return Math.ceil(this.filteredProducts.length / this.productsOnPage);
    },
    displayedItems() {
      let list = this.filterProducts(this.items);
			return this.paginate(list);
		}
  }
}
</script>

<style>
.products {
  max-width: 900px;
  margin: 0 auto;
}

.filters{
  display: flex;
  padding-bottom: 10px;
  justify-content: flex-end;
}

.filters__inputs {
  display: flex;
  justify-content: space-between;
  max-width: 800px;
  margin: 0 auto;
}

label {
  border-left: 1px solid #c5c5c5;
  padding: 0 2px;
}

input {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 5px 10px;
  margin: 0 7px;
  width: 15%;
}

.table {
  width: 100%;
  table-layout: fixed;
}

th, td {
  min-width: 90px;
  padding: 10px 20px;
}

td {
  background-color: #f9f9f9;
}

th {
  background-color: #c5c5c5;
}

.navigation {
  display: flex;
  justify-content: space-around;
  width: 100%;
}

.table__pagination {
  display: flex;
  justify-content: center;
  margin: 10px auto;
}

.table__pagination .active_page {
  background-color: #c5c5c5;
}

.table__reset_filter {
  align-self: center;
}

button{
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 5px 10px;
  background-color: #f9f9f9;
  border: 1px solid #c5c5c5;
}

button:hover{
  background-color: #c5c5c5;
  cursor: pointer;
}
</style>
