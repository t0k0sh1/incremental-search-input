<template>
  <div id="app">
    <IncrementalSearchInput
        v-model="selectedId"
        :suggestionList="suggestionList"
        class="custom-container-class"
        input-class="custom-input-class"
    />
    {{ selectedId }} {{ suggestionList.find(item => item.id === selectedId)?.text }}
  </div>
</template>

<script>
import IncrementalSearchInput from "@/components/IncrementalSearchInput.vue";

export default {
  name: 'App',
  components: {
    IncrementalSearchInput
  },
  data() {
    return {
      selectedId: null,
      suggestionList: [
        { id: 1, text: 'Apple' },
        { id: 2, text: 'Banana' },
        { id: 3, text: 'Cherry' },
        { id: 4, text: 'Date' },
        { id: 5, text: 'Elderberry' },
        { id: 6, text: 'Fig' },
        { id: 7, text: 'Grape' },
        { id: 8, text: 'Honeydew' },
        { id: 9, text: 'Kiwi' },
        { id: 10, text: 'Lemon' },
        { id: 11, text: 'Mango' },
        { id: 12, text: 'Nectarine' },
        { id: 13, text: 'Orange' },
        { id: 14, text: 'Peach' },
        { id: 15, text: 'Quince' },
        { id: 16, text: 'Raspberry' },
        { id: 17, text: 'Strawberry' },
        { id: 18, text: 'Tangerine' },
        { id: 19, text: 'Ugli fruit' },
        { id: 20, text: 'Vanilla bean' },
        { id: 21, text: 'Watermelon' },
        { id: 22, text: 'Xylocarp' },
        { id: 23, text: 'Yuzu' },
        { id: 24, text: 'Zucchini' }
      ]
    };
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.custom-input-class {
  width: 100%;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}
.custom-container-class {
  background-color: red;
}
</style>
