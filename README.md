<img src="https://www.mintjams.jp/img/cr.svg" alt ="" width="64">

# vue-filterable-selection
A reusable FilterableSelection component for Vue.js 2.x used by webtop applications.

## Installation

```sh
npm install --save-dev @mintjamsinc/vue-filterable-selection
```

## Usage

```vue
<FilterableSelection
  ref="groups"
  :multiple="true"
  :hasSearch="true"
  maxLabelWidth="10em"
  :invalid="isDirty && $validator.hasErrors('editor', 'groups')"
  v-hook="{inserted: onGroupsLoad}"/>
```

```js
import FilterableSelection from '@mintjamsinc/vue-filterable-selection';

export default {
  components: {
    FilterableSelection,
  },
  methods: {
    onGroupsLoad() {
      // let ui = vm.$refs.groups.ui;
      // ui.getIdentifier = function(o) {};
      // ui.getAuthorizable = function(o) {};
      // ui.getItem = function(o) {};
      // ui.getIcon = function(o) {};
      // ui.getLabel = function(o) {};
      // ui.comparator = function(a, b) {};
      // ui.isMatched = function(o) {};
      // ui.onChanged = function() {};
      // ui.onSearch = function() {};
      // ui.objects = ...
      // ui.selection = ...
      // ui.filterText = '';
      // ui.expanded = true;
    },
  },
}
```

## License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2021 MintJams Inc.