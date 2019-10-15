<template>
    <v-app>
        <v-content>
            <v-container
                    class="fill-height"
                    fluid
            >
                <v-row
                        align="center"
                        justify="center"
                >
                    <v-col sm="12" md="6">
                        <h1 class="text-center">Google map demo</h1>
                        <GMap
                                :items="[
                                {
                                    name: 'Hala z prysznicem',
                                    facilities: [ 'prysznic' ],
                                    lat: 52.237049,
                                    lng: 21.017532,
                                },{
                                    name: 'Hala z wanną',
                                    facilities: [ 'wanna' ],
                                    lat: 49.882786,
                                    lng: 19.493958,
                                }
                            ]"
                                :filters="[
                                {
                                    name: 'Udogodnienia',
                                    items: [
                                        {
                                            label: 'Prysznic',
                                            value: 'prysznic'
                                        },
                                        {
                                            label: 'Wanna',
                                            value: 'wanna'
                                        }
                                    ],
                                    filter: (item, selected)=>{
                                        return selected.every( x => item.facilities.some( y => y === x))
                                    }
                                }
                            ]"
                                :minimal-map-lat="52.237049"
                                :minimal-map-lng="21.017532"
                                api-key="AIzaSyA6JVSpeceHjUZCvHJZtk8vTDlzlzjrRBI"
                        >
                            <template v-slot:item="slotProps">
                                {{slotProps.item.name}}
                            </template>
                        </GMap>
                    </v-col>
                </v-row>
            </v-container>
        </v-content>
    </v-app>
</template>

<script>
	import GMap from "./components/GMap";

	export default {
		name: "App",
		components: {
			GMap
		},
		data: () => ({
			//
		})
	};
</script>
