<template>
  <div class="search-box">
    <input
      ref="input"
      aria-label="Search"
      :value="query"
      :class="{ focused: focused }"
      :placeholder="placeholder || 'Search'"
      autocomplete="off"
      spellcheck="false"
      @input="query = $event.target.value"
      @focus="focused = true"
      @blur="focused = false"
      @keyup.enter="go(focusIndex)"
      @keyup.up="onUp"
      @keyup.down="onDown"
    />
    <ul
      v-if="showSuggestions"
      class="suggestions"
      :class="{ 'align-right': alignRight }"
      @mouseleave="unfocus"
    >
      <li
        v-for="(s, i) in suggestions"
        :key="i"
        class="suggestion"
        :class="{ focused: i === focusIndex }"
        @mousedown="go(i)"
        @mouseenter="focus(i)"
      >
        <!-- Override @vuepress/plugin-search/SearchBox.vue -->
        <a :href="s.regularPath" @click.prevent>
          <span
            v-html="s.title || s.regularPath"
            class="suggestion__title"
          ></span>
          <span v-html="s.text" class="suggestion__result"></span>
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
import VuepressSearchBox from "@vuepress/plugin-search/SearchBox.vue";
import { Document } from "flexsearch";
import { highlightText } from "./utils";

/* global
SEARCH_MAX_SUGGESTIONS
SEARCH_PATHS
SEARCH_HOTKEYS
SEARCH_OPTIONS
SEARCH_RESULT_LENGTH
SEARCH_SPLIT_HIGHLIGHTED_WORDS
*/
export default {
  extends: VuepressSearchBox,
  data() {
    return {
      index: null,
    };
  },

  computed: {
    // Override @vuepress/plugin-search/SearchBox.vue
    suggestions() {
      const query = this.query.trim().toLowerCase();
      if (!query || !this.index) {
        return;
      }

      const results = this.index
        .search(query, { enrich: true })
        .reduce(
          (prev, current) => {
            const combinedResults = [...prev.result, ...current.result];
            const uniqueResults = Array.from(
              new Set(combinedResults.map((i) => JSON.stringify(i)))
            ).map((i) => JSON.parse(i));
            return { result: uniqueResults };
          },
          { result: [] }
        )
        .result.map((match) => match.doc)
        .map((page) => {
          return {
            ...page,
            title: this.getSuggestionTitle(page),
            text: this.getSuggestionText(page),
          };
        });
      return results;
    },

    splitBy() {
      return SEARCH_SPLIT_HIGHLIGHTED_WORDS;
    },
  },

  mounted() {
    this.setupFlexSearch();
  },

  methods: {
    // Override @vuepress/plugin-search/SearchBox.vue
    go(i) {
      if (!this.showSuggestions) {
        return;
      }
      const path = this.suggestions[i].path;

      if (this.$route.path !== path) {
        this.$router.push(this.suggestions[i].path);
      }

      this.query = "";
      this.focusIndex = 0;
    },

    setupFlexSearch() {
      this.index = new Document({
        ...SEARCH_OPTIONS,
        document: {
          id: "key",
          index: ["title", "content"],
          store: true,
        },
      });
      const { pages } = this.$site;
      pages.forEach((page) => {
        this.index.add(page.key, page);
      });
    },

    getSuggestionTitle(page) {
      const title = page.title ? page.title : page.regularPath;
      return highlightText(title, this.query, this.splitBy);
    },

    getSuggestionText(page) {
      const content = page.content;
      const queryIndex = content
        .toLowerCase()
        .indexOf(this.query.toLowerCase());
      const queryFirstWord = this.query.split(this.splitBy)[0];
      let startIndex =
        queryIndex === -1
          ? content.toLowerCase().indexOf(queryFirstWord.toLowerCase())
          : queryIndex;
      let prefix = "";
      if (startIndex > 15) {
        startIndex -= 15;
        prefix = ".. ";
      }
      const text = page.content.substr(startIndex, SEARCH_RESULT_LENGTH);
      return prefix + highlightText(text, this.query, this.splitBy);
    },
  },
};
</script>

<style lang="stylus">
// Override @vuepress/plugin-search/SearchBox.vue
.search-box {
  input {
    border-radius: 0.4rem;

    &:focus {
      width: 15rem;
    }
  }

  .suggestions {
    top: 1.5rem;
    border-radius: 0.6rem;
  }

  .suggestion {
    padding: 0.6rem 1rem;

    a {
      em {
        color: $accentColor;
        font-weight: bold;
        font-style: normal;
      }

      .suggestion__title {
        font-weight: 600;
        color: $textColor;
        display: block;
        padding-bottom: 0.4rem;
      }

      .suggestion__text {
        font-size: 0.9em;
      }
    }

    &.focused {
      background-color: lighten($accentColor, 93%);
    }
  }
}
</style>
