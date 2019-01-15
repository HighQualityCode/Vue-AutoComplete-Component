<template>
  <div class="autocomplete">
    <div class="search">
      <input
        type="text"
        v-model="search"
        placeholder="Ex: Alaska"
        @input="onChange"
        @click="onChange"
        @keyup.down="onArrowDown"
        @keyup.up="onArrowUp"
        @keyup.enter="onEnter"
      />

      <template v-if="search">
        <button class="clear" :class="{closing: closing}" @mousedown="setCloseBtn" @mouseup="clear">
          <!-- <img src="../assets/baseline-clear-24px.svg" /> -->
          <md-icon>clear</md-icon>
          <div class="closing-button"></div>
        </button>
      </template>
    </div>

    <ul id="autocomplete-results" v-show="isOpen" class="autocomplete-results">
      <li class="loading" v-if="isLoading">
        Loading results...
      </li>
      <li v-else
        v-for="(result, i) in results"
        :key="i"
        @click="setResult(result)"
        class="autocomplete-result"
        :class="{ 'is-active': i === arrowCounter }"
      >
        {{ result }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'AutoComplete',
  props: ['items'],
  data() {
    return {
      isOpen: false,
      results: [],
      search: '',
      isLoading: false,
      arrowCounter: 0,
      closing: false,
    };
  },

  methods: {
    onChange() {
      this.$emit('input', this.search);
      if (this.isAsync) {
        this.isLoading = true;
      } else {
        this.filterResults();
        this.isOpen = true;
      }
    },

    filterResults() {
      this.results = this.items.filter(
        item => item.toLowerCase().startsWith(this.search.toLowerCase()) === true,
      );
    },

    setResult(result) {
      this.search = result;
      this.isOpen = false;
    },

    onArrowDown() {
      if (this.arrowCounter < this.results.length) {
        this.arrowCounter = this.arrowCounter + 1;
      }
    },

    onArrowUp() {
      if (this.arrowCounter > 0) {
        this.arrowCounter = this.arrowCounter - 1;
      }
    },

    onEnter() {
      this.search = this.results[this.arrowCounter];
      this.isOpen = false;
      this.arrowCounter = -1;
    },

    handleClickOutside(evt) {
      if (!this.$el.contains(evt.target)) {
        this.isOpen = false;
        this.arrowCounter = -1;
      }
    },

    setCloseBtn() {
      this.closing = true;
    },

    clear() {
      this.closing = false;
      this.search = '';
    },
  },

  watch: {
    items: function (val, oldValue) {
      if (val.length !== oldValue.length) {
        this.results = val;
        this.isLoading = false;
      }
    },
  },

  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },

  destroyed() {
    document.removeEventListener('click', this.handleClickOutside);
  },
};
</script>

<style scoped>
.autocomplete {
  position: relative;
  width: 100%;
  box-shadow: 0 1px 3px 0 rgba(0,0,0,.2), 0 1px 1px 0 rgba(0,0,0,.14), 0 2px 1px -1px rgba(0,0,0,.12);
  border-radius: 2px;
  min-width: 190px;
  height: 40px;
}

.autocomplete .search {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.autocomplete .search > input {
  font-size: 14px;
  box-sizing: border-box;
  border: none;
  box-shadow: none;
  outline: none;
  background: transparent;
  width: 100%;
  padding: 0 15px;
  line-height: 40px;
  height: 40px;
  flex: 1 1 0%;
  min-width: 0;
  color: rgba(0, 0, 0, 0.87);
}

.autocomplete .search > button.clear {
  position: relative;
  line-height: 20px;
  text-align: center;
  width: 30px;
  height: 30px;
  cursor: pointer;
  border: none;
  border-radius: 50%;
  padding: 0;
  font-size: 12px;
  background: transparent;
  margin: auto 5px;
}

.clear.closing {
  position: relative;
}

.clear.closing .closing-button {
  position: absolute;
  top: -6px;
  right: -6px;
  bottom: -6px;
  left: -6px;
  border-radius: 50%;
  transition: all .4s cubic-bezier(0.25, 0.8, .025, 1);
  background: #eeeeee;
  z-index: -1;
}

.autocomplete-results {
  padding: 0;
  margin: 0;
  max-height: 240px;
  overflow: auto;
  width: 100%;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.25);
  border-radius: 2px;
  min-width: 190px;
}

.autocomplete-result {
  cursor: pointer;
  font-size: 14px;
  overflow: hidden;
  padding: 0 15px;
  line-height: 48px;
  height: 48px;
  transition: background .15s linear;
  margin: 0;
  white-space: nowrap;
  text-overflow: ellipsis;
  color: rgba(0, 0, 0, 0.87);
  list-style: none;
  text-align: left;
  cursor: pointer;
}

.autocomplete-result.is-active,
.autocomplete-result:hover {
  background-color: #efefef;
}
</style>
