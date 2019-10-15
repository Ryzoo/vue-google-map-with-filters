<template>
    <section id="mapSection">
        <div v-show="!isLoading" id="minimalMap"></div>
        <Loader v-show="isLoading"></Loader>
        <v-fade-transition mode="in-out">
            <GMapFull
                :minimal-map-lng="minimalMapLng"
                :minimal-map-lat="minimalMapLat"
                :minimal-map-zoom="minimalMapZoom"
                v-if="showFullMap"
                :items="items"
                :filters="filters"
                @close="()=>{this.showFullMap = false}"
            >
                <template v-slot:item="{ item }">
                    <slot name="item" :item="item"></slot>
                </template>
            </GMapFull>
        </v-fade-transition>
    </section>
</template>

<script>
	import Loader from "./Loader";
	import GMapFull from "./GMapFull";

	export default {
		props: {
			apiKey: {
				type: String,
				required: true
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
			},
			langMinimalButtonDescription: {
				type: String,
				default: "Pokaż zawartość w osobnym oknie"
			},
			langMinimalButtonTitle: {
				type: String,
				default: "Szukaj na mapie"
			},
			items: {
				type: Array,
				default: ()=>{return []}
			},
			filters: {
				type: Array,
				default: ()=>{return []}
			},
		},
		components: {
			Loader,
			GMapFull
		},
		data() {
			return {
				googleMapsApiPromise: null,
				googleMapsScriptIsInjected: null,
				isLoading: true,
                maps: null,
				minimalMap: null,
				showFullMap: false
			}
		},
		methods: {
			injectGoogleMapsApiScript(options = {}) {
				if (this.googleMapsScriptIsInjected) {
					throw new Error('Google Maps Api is already loaded.');
				}

				const optionsQuery = Object.keys(options)
					.map(k => `${encodeURIComponent(k)}=${encodeURIComponent(options[k])}`)
					.join('&');

				const url = `https://maps.googleapis.com/maps/api/js?${optionsQuery}`;

				const script = document.createElement('script');

				script.setAttribute('src', url);
				script.setAttribute('async', '');
				script.setAttribute('defer', '');

				document.head.appendChild(script);

				this.googleMapsScriptIsInjected = true;
			},
			loadGoogleMapsApi(apiKey, options = {}) {
				if (!this.googleMapsApiPromise) {
					this.googleMapsApiPromise = new Promise((resolve, reject) => {
						try {
							window.onGoogleMapsApiLoaded = resolve;

							this.injectGoogleMapsApiScript({
								key: apiKey,
								callback: 'onGoogleMapsApiLoaded',
								...options,
							});
						} catch (error) {
							reject(error);
						}
					}).then(() => this.maps = window.google.maps);
				}

				return this.googleMapsApiPromise;
			},
			init() {
				this.isLoading = false;
				this.$nextTick(this.createMinimalMap);
			},
			createMinimalMap(){
				this.minimalMap = new this.maps.Map(document.getElementById('minimalMap'), {
					center: {lat: this.minimalMapLat, lng: this.minimalMapLng},
					zoom: this.minimalMapZoom,
					gestureHandling: 'cooperative',
					disableDefaultUI: true
				});

				let customButton = this.getCustomControlButton();
				this.minimalMap.controls[this.maps.ControlPosition.TOP_RIGHT].push(customButton);
            },
            getCustomControlButton(){
				let controlDiv = document.createElement('div');
				controlDiv.index=1;

				let controlUI = document.createElement('div');
				controlUI.style.backgroundColor = '#2196F3';
				controlUI.style.borderRadius = '2px';
				controlUI.style.cursor = 'pointer';
				controlUI.style.margin = '15px 15px 0 0';
				controlUI.style.textAlign = 'center';
				controlUI.style.padding = '0px 10px';
				controlUI.title = this.langMinimalButtonDescription;
				controlDiv.appendChild(controlUI);

				let controlText = document.createElement('div');
				controlText.style.color = 'white';
				controlText.style.fontFamily = 'Roboto,Arial,sans-serif';
				controlText.style.fontSize = '16px';
				controlText.style.lineHeight = '38px';
				controlText.style.paddingLeft = '5px';
				controlText.style.paddingRight = '5px';
				controlText.innerHTML = this.langMinimalButtonTitle;
				controlUI.appendChild(controlText);

				controlUI.addEventListener('click', ()=>{
					this.showFullMap = true
                });

				return controlDiv;
            }
		},
		mounted() {
			this.loadGoogleMapsApi(this.apiKey)
				.then(this.init);
		}
	};
</script>
<style lang="scss" scoped>
    #mapSection{
        width: 100%;
        height: auto;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;

        #minimalMap{
            width: 100%;
            height: 400px;
        }
    }
</style>
