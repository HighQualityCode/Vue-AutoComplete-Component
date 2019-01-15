<template>
  <div class="autocomplete-test">
    <AutoComplete :items="autoItems" />
  </div>
</template>

<script>
import AutoComplete from '../components/AutoComplete';

export default {
  name: 'Index',
  components: {
    AutoComplete
  },
  data() {
    return {
      autoItems: [
        'Apple',
        'Banana',
        'Orange',
        'Mango',
        'Pear',
        'Peach',
        'Grape',
        'Tangerine',
        'Pineapple'
      ]
    }
  }
};
</script>

<style scoped>
.autocomplete-test {
  padding: 100px;
}
</style>
