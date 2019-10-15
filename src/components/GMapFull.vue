<template>
    <v-dialog :value="true" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-card class="gmap-card">
            <GMapFilters
                :lang-search-input="langSearchInput"
                :filters="filters"
                @filterChange="filterChange"
            ></GMapFilters>

            <section class="full-map-content fill-height">
                <v-toolbar style="flex: 0;" dark color="primary">
                    <v-btn icon dark @click="closeDialog">
                        <v-icon>mdi-close</v-icon>
                    </v-btn>
                    <v-toolbar-title>{{this.langFullMapTitle}}</v-toolbar-title>
                    <v-spacer></v-spacer>
                    <v-toolbar-items>
                        <v-btn dark text @click="closeDialog">{{this.langFullMapCloseButtonTitle}}</v-btn>
                    </v-toolbar-items>
                </v-toolbar>
                <GMapBaseMap
                    @boundsChange="boundsChange"
                    :minimal-map-lng="minimalMapLng"
                    :minimal-map-lat="minimalMapLat"
                    :minimal-map-zoom="minimalMapZoom"
                    :items="filteredItems"
                ></GMapBaseMap>
            </section>

            <GMapAvailableItems
                :items="filteredItemsWithView"
            >
                <template v-slot:item="{ item }">
                    <slot name="item" :item="item"></slot>
                </template>
            </GMapAvailableItems>
        </v-card>
    </v-dialog>
</template>

<script>
	import GMapFilters from "./GMapFilters";
	import GMapAvailableItems from "./GMapAvailableItems";
	import GMapBaseMap from "./GMapBaseMap";

	export default {
		name: "GMapFull",
        components:{
			GMapFilters,
			GMapAvailableItems,
			GMapBaseMap
        },
        data(){
		    return {
		    	currentFilters: this.filters,
				bounds: null
            }
        },
		props: {
			minimalMapLat: {
				type: Number,
				required: true
			},
			minimalMapLng: {
				type: Number,
				required: true
			},
			minimalMapZoom: {
				type: Number,
				default: 8
			},
			langFullMapTitle: {
				type: String,
				default: "Pełna mapa obiektów"
			},
			langFullMapCloseButtonTitle: {
				type: String,
				default: "Zamknij"
			},
			items: {
				type: Array,
				default: () => {
					return []
				}
			},
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
        computed:{
			filteredItems(){
				return this.items.filter(x => {
					return this.currentFilters.every(f => {
						const selected = f.items.filter(i => i.selected).map(i => i.value);
						return f.filter(x, selected);
                    });
                });
            },
            filteredItemsWithView(){
				return this.bounds ?
                    this.filteredItems.filter( x =>
                        x.lat >= this.bounds.swLat && x.lat <= this.bounds.neLat
                        && x.lng >= this.bounds.swLng && x.lng <= this.bounds.neLng)
                    : this.filteredItems;
            }
        },
		methods: {
			boundsChange(bounds){
				this.bounds = bounds;
            },
			filterChange(filters){
				this.currentFilters = Object.assign([], filters);
            },
			closeDialog() {
				this.$emit("close");
			}
		}
	}
</script>

<style scoped>
    .full-map-content{
        display: flex;
        flex: 1;
        flex-direction: column;
        height: auto;
    }
    .gmap-card{
        overflow: hidden;
        display: flex;
        flex-direction: row;
        justify-content: stretch;
        align-items: stretch;
    }
</style>