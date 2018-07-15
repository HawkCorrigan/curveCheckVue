/* eslint-disable */
<template>
  <div class="autocomplete">
    <input
      type="text"
        v-model="search"
        @input="onChange"
      />
      <ul
        v-show="isOpen"
        class="autocomplete-results">
        <li
          v-for="(result, i) in results"
          :key="i"
          class="autocomplete-result">
          {{result}}
        </li>
      </ul>
    </div>
</template>

<script>
/* eslint-disable */
  export default {
    name: "Autocomplete",
    props: {
      items: {
        type: Array,
        required: false,
        default: ()=>[],
      },
    },
    data() {
      return {
        search: '',
      };
    },
    methods: {
      onChange() {
        this.isOpen=true;
        this.filterResults();
      },
      filterResults(){
        this.results = this.items.filter(item => item.toLowerCase().indexOf(this.search.toLowerCase())>-1);
      }
    },

  }
</script>

<style scoped>
  .autocomplete {
    position: relative;
    width : 130px;
  }
  .autocomplete-results {
    padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    height: 120px;
    overflow: auto;
  }

  .autocomplete-result {
    list-style: none;
    text-align: left;
    padding: 4px 2px;
    cursor: pointer;
  }

  .autocomplete-result:hover {
    background-color: #4aae9b;
    color: white;
  }
</style>
