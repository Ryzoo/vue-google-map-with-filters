<template>
    <div class="gmaps-available-items">
        <div
                @click="()=>{centerOn(item)}"
                :class="{selected: item.selected, 'item-element': true}"
                v-for="item in itemsList">
            <slot
                name="item"
                :item="item"
            ></slot>
        </div>
    </div>
</template>

<script>
	export default {
		name: "GMapAvailableItems",
		props: {
			items: {
				type: Array,
				default: () => {
					return []
				}
			},
		},
        watch:{
			items(){
				this.setItems();
            }
        },
        data(){
			return {
				itemsList: [],
                selected: null
            }
        },
		methods: {
			setItems(){
				this.itemsList = this.items.map( x => {
					return {
						...x,
						selected: this.isSameItem(x, this.selected)
					}
				});
			},
			centerOn(item) {
				this.selected = item;

				this.itemsList = this.itemsList.map(x => {
					x.selected = this.isSameItem(x, item);
					return x;
                });

				this.$emit("centerOnItem", item);
			},
			isSameItem(x, item){
				if(!x || !item) return false;
				return x.lat === item.lat && x.lng === item.lng && x.name === item.name;
            }
		},
        mounted() {
			this.setItems();
		}
	}
</script>

<style scoped lang="scss">
    .gmaps-available-items {
        @media (max-width: 900px) {
            display: none;
        }
        width: 215px;
        padding: 10px;
        height: auto;
        overflow-x: hidden;
        overflow-y: scroll;
    }

    .item-element {
        border: 2px solid #e8e8e8;
        padding: 5px;
        display: flex;
        margin: 5px 0;
        border-radius: 5px;
        justify-content: center;
        align-items: center;
        transition: all .2s;


        &.selected{
            border: 2px solid #e8f09b;
            &:hover {
                cursor: pointer;
                border: 2px solid #e8f09b;
            }
        }

        &:hover {
            cursor: pointer;
            border: 2px solid #c0c0c0;
        }
    }
</style>