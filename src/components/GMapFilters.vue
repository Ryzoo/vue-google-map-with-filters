<template>
    <div class="gmaps-filters">
        <v-text-field
            :label="this.langSearchInput"
            v-model="search"
            hide-details
            solo
            clearable
            class="mt-3 mb-5"
        ></v-text-field>
        <section class="filter-elements" v-for="filter in filtersList">
            <h2>{{filter.name}}</h2>
            <v-checkbox
                    v-for="item in filter.items"
                    v-show="(search && search.length > 1) ? item.label.toLowerCase().includes(search.toLowerCase()) : true"
                    hide-details
                    v-model="item.selected"
                    :label="item.label"
                    :key="item.value+item.label"
                    @change="filterChange"
            ></v-checkbox>
            <v-divider class="my-3"></v-divider>
        </section>
    </div>
</template>

<script>
	export default {
		name: "GMapFilters",
		props: {
			filters: {
				type: Array,
				default: () => {
					return []
				}
			},
			langSearchInput: {
				type: String,
				default: "Szukaj"
			}
		},
        data(){
		    return {
				filtersList: [],
				search: ""
            }
        },
        methods:{
			initFilters(){
				this.filtersList = this.filters.map(x => {
					let elements = x.items.map(r => {
						return {
							...r,
                            selected: false
                        }
                    });
					return {
						...x,
                        items: elements,
                    }
                });
            },
            filterChange(){
				this.$emit('filterChange', this.filtersList)
            }
        },
        mounted() {
			this.initFilters();
		}
	}
</script>

<style scoped>
    .gmaps-filters{
        width: 215px;
        padding: 10px;
        height: auto;
        overflow-x: hidden;
        overflow-y: scroll;
    }
</style>