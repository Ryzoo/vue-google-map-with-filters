<template>
    <div class="gmaps-base-map">
        <div id="fullBaseMap"></div>
    </div>
</template>

<script>
	export default {
		name: "GMapBaseMap",
		props: {
			items: {
				type: Array,
				default: () => {
					return []
				}
			},
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
			}
		},
        data(){
			return {
				maps: window.google.maps,
				fullMap: null,
                markers: []
            }
        },
        watch:{
			items(){
				this.buildMarkers();
            }
        },
        methods:{
			centerOnItem(item){
				if(!item) return;

			    let indexOfMarker = -1;

			    this.items.map((x, index) => {
			    	if(x.lat === item.lat && x.lng === item.lng){
						indexOfMarker = index;
                    }
                });

			    if(indexOfMarker < 0)
			    	return;

				this.markers.map(x => x.setAnimation(null));
			    this.markers[indexOfMarker]
                    .setAnimation(this.maps.Animation.BOUNCE);
				this.fullMap.panTo(this.markers[indexOfMarker].getPosition());
            },
			loadMap(){
				this.fullMap = new this.maps.Map(document.getElementById('fullBaseMap'), {
					center: {lat: this.minimalMapLat, lng: this.minimalMapLng},
					zoom: this.minimalMapZoom,
					gestureHandling: 'cooperative',
					disableDefaultUI: true
				});
				this.maps.event.addListener(this.fullMap, 'bounds_changed', this.boundsAreChanged);
				this.buildMarkers();
            },
            boundsAreChanged(){
                let bounds = this.fullMap.getBounds();
                this.$emit("boundsChange",{
					neLat: bounds.getNorthEast().lat(),
					neLng: bounds.getNorthEast().lng(),
					swLat: bounds.getSouthWest().lat(),
					swLng: bounds.getSouthWest().lng()
                });
            },
			centerOnMarker(marker){
                let item = this.getItemFromMarker(marker);

                if(item)
                    this.$emit('centerOnItem', item);
            },
			getItemFromMarker(marker){
			    return this.items.find(x => x === marker.item);
            },
            buildMarkers(){
				this.resetMarkers();
				this.items.map(i => {
					let marker = new this.maps.Marker({
						position: {
							lat: i.lat,
                            lng: i.lng
                        },
						title: i.name,
                        item: i
					});
					marker.setMap(this.fullMap);
					this.maps.event
                        .addListener(marker, 'click', ()=>{
                        	this.centerOnMarker(marker);
                        });
					this.markers.push(marker);
                })
            },
			resetMarkers(){
				this.markers.map(e=>{
					e.setMap(null);
					this.maps.event.clearListeners(e, 'click');
				}) ;
				this.markers = [];
            }
        },
        mounted() {
			this.loadMap();
		}
	}
</script>

<style scoped>
    .gmaps-base-map {
        width: 100%;
        height: auto;
        position: relative;
        overflow: hidden;
        flex: 1;
    }

    .gmaps-base-map:before, .gmaps-base-map:after, #fullBaseMap:before, #fullBaseMap:after {
        position: absolute;
        display: block;
        box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.4);
        content: '';
        z-index: 5;
    }
    .gmaps-base-map:before {
        top: -5px;
        left: 0;
        right: 0;
        height: 5px;
    }
    .gmaps-base-map:after {
        right: -5px;
        top: 0;
        bottom: 0;
        width: 5px;
    }

    #fullBaseMap:before{
        bottom: -5px;
        left: 0;
        right: 0;
        height: 5px;
    }
    #fullBaseMap:after{
        left: -5px;
        top: 0;
        bottom: 0;
        width: 5px;
    }
    #fullBaseMap {
        width: 100%;
        height: 100%;
    }
</style>