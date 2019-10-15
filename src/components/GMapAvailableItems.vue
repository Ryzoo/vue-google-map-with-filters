<template>
    <div class="gmaps-available-items">
        <template v-for="item in items">
            <slot
                name="item"
                :item="item"
            ></slot>
        </template>
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
	}
</script>

<style scoped>
    .gmaps-available-items{
        width: 215px;
        padding: 10px;
        height: auto;
        overflow-x: hidden;
        overflow-y: scroll;
    }
</style>