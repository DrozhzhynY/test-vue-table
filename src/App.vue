<template>
  <div id="app" class="app">
    <button @click="getData" class="app__btn">Get data</button>
    <p v-if="showError && sortedStocks.length === 0">no data</p>

    <div v-if="loading">
      <p>loading...</p>
    </div>

    <div v-else-if="sortedStocks.length">
      <div class="app__table">
        <div class="app__table_head">
          <div>Stocks</div>
          <div>Current</div>
          <div>Change</div>
        </div>
        <div class="app__table_body">
          <div class="app__table_col">
            <div v-for="stock in sortedStocks" :key="stock" class="app__table_cell">
              {{ stock }}
            </div>
          </div>
          <div class="app__table_col">
            <div v-for="current in data.current" :key="current" class="app__table_cell">
              {{ current.toFixed(2) }}
            </div>
          </div>
          <div class="app__table_col">
            <div v-for="change in data.start" :key="change" class="app__table_cell">
            <span
              class="app__table_change-item"
              :class="{redText: change < 0}"
            >
              {{ change.toFixed(2) }}
            </span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-else>
      <p>click on the button</p>
    </div>

  </div>
</template>

<script>
import {payload} from '@/mocData'
import simulateAsyncReq from "@/plugins/getDataFunc";

export default {
  name: 'App',
  data() {
    return {
      data: {},
      sortedStocks: [],
      loading: false,
      showError: false,
    }
  },
  methods: {
    getData() {
      this.loading = true
      simulateAsyncReq(payload)
        .then((response) => {
          this.data = response;
          this.sortedStocks = response.stocks.sort((a, b) => a.localeCompare(b))
          return response;
        })
        .catch((error) => {
          this.showError = true;
          return error;
        })
          .finally(() => (this.loading = false))
    },
  },
}
</script>

<style lang="scss" scoped>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
}

.app {
  margin: 60px auto;
  padding: 40px 10px 100px;
  max-width: 600px;
  text-align: center;
  border: 3px solid #F8F9FA;
  &__btn {
    margin-bottom: 30px;
    height: 40px;
    min-width: 130px;
    font-weight: bold;
    border: 2px solid #EDEDED;
    background-color: #F5F5F5;
    cursor: pointer;
    transition: 0.3s;
    &:hover {
      background-color: #FFFFFF;
    }
  }
  &__table {
    margin: auto;
    max-width: 400px;
    border: 2px solid #EDEDED;
    border-radius: 5px;
  }
  &__table_head {
    display: flex;
    flex-wrap: wrap;
    height: 40px;
    align-items: center;
    background-color: #F5F5F5;
    & div {
      flex-grow: 1;
      font-weight: bold;
    }
  }
  &__table_body {
    display: flex;
    flex-wrap: wrap;
  }
  &__table_col {
    flex-grow: 1;
  }
  &__table_cell {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 40px;
    border-bottom: 2px solid #EDEDED;
    &:last-child {
      border-bottom: none;
    }
  }
  &__table_change-item {
    color: #9EC679;
    &.redText {
      color: crimson;
    }
  }
}
</style>
