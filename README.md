[![https://badgen.net/npm/v/vuepress-plugin-flexsearch](https://badgen.net/npm/v/vuepress-plugin-flexsearch)](https://www.npmjs.com/package/vuepress-plugin-flexsearch)
[![https://badgen.net/npm/dt/vuepress-plugin-flexsearch](https://badgen.net/npm/dt/vuepress-plugin-flexsearch)](https://www.npmjs.com/package/vuepress-plugin-flexsearch)
[![https://badgen.net/npm/license/vuepress-plugin-flexsearch](https://badgen.net/npm/license/vuepress-plugin-flexsearch)](https://github.com/z3by/vuepress-plugin-flexsearch/blob/master/LICENSE)
[![https://badgen.net/github/contributors/z3by/vuepress-plugin-flexsearch](https://badgen.net/github/contributors/z3by/vuepress-plugin-flexsearch)](https://github.com/z3by/vuepress-plugin-flexsearch/graphs/contributors)

# vuepress-plugin-flexsearch-7

This is a fork from [vuepress-plugin-flexsearch](https://github.com/z3by/vuepress-plugin-flexsearch)
The branch for version 7 works fine but it still did not make it to npm. So I created the fork and published
it myself.

Next-Generation full text search library for Vuepress

> A great replacement of the default Vuepress search plugin.

## Installation

```bash
yarn add -D vuepress-plugin-flexsearch-7
// or npm install vuepress-plugin-flexsearch-7 -D

```

## Usage

Use the default settings:

```js
// .vuepress/config.js

module.exports = {
    plugins: [
      ['flexsearch'],
      // other plugins
    ]
}
```

Or modify the settings to match your needs:

```js
// .vuepress/config.js

module.exports = {
    plugins: [
      ['flexsearch', {
        /*
          Plugin custom options
        */
        maxSuggestions: 10,    // how many search suggestions to show on the menu, the default is 10.
        searchPaths: ['path1', 'path2'],    // an array of paths to search in, keep it null to search all docs.
        searchHotkeys: ['s'],    // Hot keys to activate the search input, the default is "s" but you can add more.
        searchResultLength: 60,    // the length of the suggestion result text by characters, the default is 60 characters.
        splitHighlightedWords: ' ',  // regex or string to split highlighted words by, keep it null to use flexsearch.split
        noExtraSpaceAfterHtmlTag: false,   // don't add extra spaces in highlighted results
        /*
          Default FlexSearch options
          To override the default options you can see available options at https://github.com/nextapps-de/flexsearch
        */
        search_options: {
          encode: "icase",
          tokenize: "forward",
          resolution: 9,
          doc: {
            id: "key",
            field: ["title", "content", "headers"],
          }
        }
      }],
      // other plugins
    ]
}
```

## Credit

Thanks to [nextapps-de/flexsearch](https://github.com/nextapps-de/flexsearch)

## Contributions

PRs are welcome :heart:
