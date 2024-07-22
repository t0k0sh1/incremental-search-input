<template>
  <div class="autocomplete-container">
    <input
        type="text"
        :class="inputClass"
        v-model="query"
        @input="onInput"
        @focus="onFocus"
        @blur="validateSelection"
        @keydown="onKeyDown"
        ref="inputElement"
    />
    <ul v-if="showSuggestions && filteredSuggestions.length">
      <li
          v-for="(suggestion, index) in filteredSuggestions"
          :key="suggestion.id"
          @mousedown.prevent="selectSuggestion(suggestion)"
          :class="{ 'is-active': index === activeSuggestionIndex }"
          ref="suggestionElements"
      >
        {{ suggestion.text }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Number,
      default: null
    },
    suggestionList: {
      type: Array,
      required: true,
      validator(value) {
        return value.every(item => item.id !== undefined && item.text !== undefined);
      }
    },
    inputClass: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      query: '',
      selectedId: this.value,
      suggestions: this.suggestionList,
      showSuggestions: false,
      filteredSuggestions: [],
      activeSuggestionIndex: -1,
      lastValidSelection: { id: null, text: '' }
    };
  },
  watch: {
    value(newValue) {
      this.selectedId = newValue;
      this.setInitialSelection();
    }
  },
  methods: {
    onInput() {
      this.filteredSuggestions = this.suggestions.filter(suggestion =>
          suggestion.text.toLowerCase().includes(this.query.toLowerCase())
      );
      this.activeSuggestionIndex = -1; // Reset the active suggestion index
    },
    selectSuggestion(suggestion) {
      this.query = suggestion.text;
      this.selectedId = suggestion.id;
      this.lastValidSelection = {id: suggestion.id, text: suggestion.text};
      this.showSuggestions = false;
      this.$emit('input', suggestion.id);
    },
    hideSuggestions() {
      setTimeout(() => {
        this.showSuggestions = false;
      }, 100);
    },
    onKeyDown(event) {
      if (event.key === 'ArrowDown') {
        if (this.activeSuggestionIndex < this.filteredSuggestions.length - 1) {
          this.activeSuggestionIndex++;
        } else {
          this.activeSuggestionIndex = 0;
        }
        this.scrollToActiveElement();
      } else if (event.key === 'ArrowUp') {
        if (this.activeSuggestionIndex > 0) {
          this.activeSuggestionIndex--;
        } else {
          this.activeSuggestionIndex = this.filteredSuggestions.length - 1;
        }
        this.scrollToActiveElement();
      } else if (event.key === 'Enter') {
        if (this.activeSuggestionIndex >= 0 && this.activeSuggestionIndex < this.filteredSuggestions.length) {
          this.selectSuggestion(this.filteredSuggestions[this.activeSuggestionIndex]);
        }
      }
    },
    scrollToActiveElement() {
      this.$nextTick(() => {
        const activeElement = this.$refs.suggestionElements[this.activeSuggestionIndex];
        if (activeElement) {
          activeElement.scrollIntoView({block: 'nearest'});
        }
      });
    },
    onFocus() {
      if (this.filteredSuggestions.length === 0) {
        this.filteredSuggestions = this.suggestions.filter(suggestion =>
            suggestion.text.toLowerCase().includes(this.query.toLowerCase())
        );
      }
      this.showSuggestions = true;
    },
    validateSelection() {
      if (this.query === '') {
        this.selectedId = null;
        this.lastValidSelection = {id: null, text: ''};
        this.$emit('input', null);
      } else {
        const isValid = this.suggestions.some(suggestion => suggestion.text === this.query);
        if (!isValid) {
          this.query = this.lastValidSelection.text;
          this.selectedId = this.lastValidSelection.id;
          this.$emit('input', this.lastValidSelection.id);
        }
      }
      this.hideSuggestions();
    },
    setInitialSelection() {
      if (this.selectedId !== null) {
        const initialSuggestion = this.suggestions.find(suggestion => suggestion.id === this.selectedId);
        if (initialSuggestion) {
          this.query = initialSuggestion.text;
          this.lastValidSelection = {id: initialSuggestion.id, text: initialSuggestion.text};
        } else {
          this.query = '';
          this.lastValidSelection = {id: null, text: ''};
        }
      } else {
        this.query = '';
        this.lastValidSelection = {id: null, text: ''};
      }
    }
  },
  mounted() {
    this.setInitialSelection();
  }
};
</script>

<style scoped>
.autocomplete-container {
  position: relative;
  display: inline-block;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
  border: 1px solid #ccc;
  position: absolute;
  z-index: 1000;
  background-color: white;
  width: 100%;
  max-height: 150px;
  overflow-y: auto;
}

li {
  padding: 8px;
  cursor: pointer;
  text-align: left;
}

li.is-active {
  background-color: #f0f0f0;
}

li:hover {
  background-color: #e0e0e0;
}
</style>