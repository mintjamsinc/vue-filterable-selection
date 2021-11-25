<!-- Copyright (c) 2021 MintJams Inc. Licensed under MIT License. -->

<template>
	<div class="position-relative flat-control flat-blue bordered rounded text-small">
		<div v-if="selected.index.length > 0" class="d-flex flex-wrap pl-2 pt-2">
			<div v-for="id in selected.index" :key="id" class="mr-2 mb-2">
				<Badge class="shadow-sm"
					:authorizable="ui.$getAuthorizable(selected.map[id])"
					:item="ui.$getItem(selected.map[id])"
					:icon="ui.$getIcon(selected.map[id])"
					:identifier="ui.$getImageIdentifier(selected.map[id])"
					:image="ui.$getImage(selected.map[id])"
					:lazyImage="ui.$getLazyImage(selected.map[id])"
					:label="ui.$getLabel(selected.map[id])"
					:maxLabelWidth="maxLabelWidth">
					<span class="ml-2 text-small text-white-50 text-shadow c-pointer" v-on:click="doUnselect(id)"><i class="fas fa-times"></i></span>
				</Badge>
			</div>
		</div>
		<div v-if="showFilter" class="d-flex justify-content-between align-items-center position-relative w-100">
			<input type="text" class="flex-grow-1 p-2 border-none outline-none" :class="{'text-shadow': filterText}" :placeholder="placeholder" name="filterText" v-model="filterText" v-on:keyup.enter.prevent.stop="doSearch" autocomplete="off">
			<div class="d-flex justify-content-end align-items-center">
				<div v-if="hasSearch" class="input-icon mr-2 text-shadow c-pointer d-flex justify-content-center align-items-center" v-on:click.prevent.stop="doSearch">
					<i class="fas fa-search"></i>
				</div>
				<div class="input-icon mr-2 text-shadow c-pointer d-flex justify-content-center align-items-center" v-on:click.prevent.stop="isExpanded = !isExpanded">
					<i v-if="!isExpanded" class="fas fa-chevron-down"></i>
					<i v-if="isExpanded" class="fas fa-chevron-up"></i>
				</div>
			</div>
		</div>
		<div v-if="showSelectableList" class="d-flex flex-wrap border-top bg-black-5 pl-2 pt-2">
			<div v-for="id in selectables" :key="id" class="mr-2 mb-2 c-pointer" v-on:click.prevent.stop="doSelect(id)">
				<Badge class="shadow-sm"
					:authorizable="ui.$getAuthorizable(objects.map[id])"
					:item="ui.$getItem(objects.map[id])"
					:icon="ui.$getIcon(objects.map[id])"
					:identifier="ui.$getImageIdentifier(selected.map[id])"
					:image="ui.$getImage(selected.map[id])"
					:lazyImage="ui.$getLazyImage(selected.map[id])"
					:label="ui.$getLabel(objects.map[id])"
					:maxLabelWidth="maxLabelWidth">
					<span v-if="multiple" class="ml-2 text-small text-white-50 text-shadow"><i class="fas fa-plus"></i></span>
				</Badge>
			</div>
			<div class="not-found p-2 text-small font-weight-semibold text-black-50 text-uppercase">No matching records found</div>
		</div>
	</div>
</template>

<script>
import Badge from '@mintjamsinc/vue-badge';

export default {
	props: {
		'placeholder': {
			'type': String,
			'default': 'type filter text'
		},
		'multiple': {
			'type': Boolean,
			'default': true
		},
		'maxLabelWidth': {
			'type': String,
			'default': '10rem'
		},
		'hasFilter': {
			'type': Boolean,
			'default': true
		},
		'hasSearch': {
			'type': Boolean,
			'default': false
		},
	},
	components: {
		Badge,
	},
	data() {
		return {
			'selected': {
				'index': [],
				'map': {}
			},
			'filterText': '',
			'isExpanded': false,
			'objects': {
				'index': [],
				'map': {}
			},
			'ui': null,
		};
	},
	created() {
		let vm = this;
		class UI {
			constructor() {
				this.$getIdentifier = function(o) {
					const authorizable = this.$getAuthorizable(o);
					if (authorizable) {
						return authorizable.id;
					}

					const item = this.$getItem(o);
					if (item) {
						return item.path;
					}

					return o;
				};
				this.$getAuthorizable = function(/* o */) {
					return undefined;
				};
				this.$getItem = function(/* o */) {
					return undefined;
				};
				this.$getIcon = function(/* o */) {
					return undefined;
				};
				this.$getImageIdentifier = function(/* o */) {
					return undefined;
				};
				this.$getImage = function(/* o */) {
					return undefined;
				};
				this.$getLazyImage = function(/* o */) {
					return undefined;
				};
				this.$getLabel = function(o) {
					const authorizable = this.$getAuthorizable(o);
					if (authorizable) {
						return undefined;
					}

					const item = this.$getItem(o);
					if (item) {
						return undefined;
					}

					return '' + o;
				};
				this.$comparator = function(a, b) {
					const _this = this;
					const _text = function(o) {
						const authorizable = _this.$getAuthorizable(o);
						if (authorizable) {
							return authorizable.fullName || authorizable.id;
						}

						const item = _this.$getItem(o);
						if (item) {
							return item.name;
						}

						return '' + o;
					};

					const a1 = _text(a);
					const b1 = _text(b);
					if (a1 < b1) {
						return -1;
					}
					if (a1 > b1) {
						return 1;
					}
					return 0;
				};
				this.$isMatched = function(/* o */) {
					return true;
				};
				this.$onChanged = function() {
					return;
				};
				this.$onSearch = function() {
					return;
				};
			}

			get selection() {
				if (vm.multiple) {
					let l = [];
					for (let identifier of vm.selected.index) {
						l.push(vm.selected.map[identifier]);
					}
					return l;
				}
				if (vm.selected.index.length == 0) {
					return undefined;
				}
				return vm.selected.map[vm.selected.index[0]];
			}
			set selection(objects) {
				if (objects == undefined) {
					objects = [];
				}
				if (!Array.isArray(objects)) {
					objects = [objects];
				}

				vm.selected = {
					'index': [],
					'map': {}
				};

				for (let obj of objects) {
					let id = this.$getIdentifier(obj);
					if (id == undefined) {
						continue;
					}
					vm.selected.index.push(id);
					vm.selected.map[id] = obj;
				}
			}

			get objects() {
				return Object.values(vm.objects.map);
			}
			set objects(objects) {
				if (objects == undefined) {
					objects = [];
				}
				if (!Array.isArray(objects)) {
					objects = [objects];
				}

				vm.objects = {
					'index': [],
					'map': {}
				};

				for (let obj of objects) {
					let id = this.$getIdentifier(obj);
					if (id == undefined) {
						continue;
					}
					vm.objects.index.push(id);
					vm.objects.map[id] = obj;
				}
			}

			get filterText() {
				return vm.filterText;
			}
			set filterText(filterText) {
				vm.filterText = filterText;
			}

			get expanded() {
				return vm.isExpanded;
			}
			set expanded(expanded) {
				vm.isExpanded = expanded;
			}

			set getIdentifier(f) {
				this.$getIdentifier = f;
			}
			set getAuthorizable(f) {
				this.$getAuthorizable = f;
			}
			set getItem(f) {
				this.$getItem = f;
			}
			set getIcon(f) {
				this.$getIcon = f;
			}
			set getImageIdentifier(f) {
				this.$getImageIdentifier = f;
			}
			set getImage(f) {
				this.$getImage = f;
			}
			set getLazyImage(f) {
				this.$getLazyImage = f;
			}
			set getLabel(f) {
				this.$getLabel = f;
			}
			set comparator(f) {
				this.$comparator = f;
			}
			set isMatched(f) {
				this.$isMatched = f;
			}
			set onChanged(f) {
				this.$onChanged = f;
			}
			set onSearch(f) {
				this.$onSearch = f;
			}
		}
		vm.ui = new UI();
	},
	computed: {
		selectables() {
			let vm = this;
			let l = [];
			for (let id of vm.objects.index) {
				if (vm.selected.index.indexOf(id) != -1) {
					continue;
				}
				if (!vm.ui.$isMatched(vm.objects.map[id])) {
					continue;
				}
				l.push(id);
			}
			l.sort(function(a, b) {
				let ao = vm.objects.map[a];
				let bo = vm.objects.map[b];
				return vm.ui.$comparator(ao, bo);
			});
			return l;
		},
		showFilter() {
			let vm = this;
			if (!vm.hasFilter) {
				return false;
			}
			if (!vm.multiple && vm.selected.index.length > 0) {
				return false;
			}
			return true;
		},
		showSelectableList() {
			let vm = this;
			if (!vm.isExpanded) {
				return false;
			}
			if (!vm.multiple && vm.selected.index.length > 0) {
				return false;
			}
			return true;
		},
	},
	methods: {
		doSelect(id) {
			let vm = this;
			if (vm.selected.index.indexOf(id) == -1) {
				vm.selected.index.push(id);
				vm.selected.map[id] = vm.objects.map[id];
			}
			vm.ui.$onChanged();
		},
		doUnselect(id) {
			let vm = this;
			let i = vm.selected.index.indexOf(id);
			if (i != -1) {
				vm.selected.index.splice(i, 1);
				delete vm.selected.map[id];
			}
			vm.ui.$onChanged();
		},
		doSearch() {
			let vm = this;
			if (!vm.hasSearch) {
				return;
			}
			vm.ui.$onSearch();
		},
	}
}
</script>
