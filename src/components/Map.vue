<template>
    <div id="map" class="h-full w-full">
    </div>
</template>

<script>
export default {
    props: {
        coords: {
            required: true,
            type: Array
        },
        newMarker: {
            type: Object
        }
    },
    data() {
        return {
            access_token: import.meta.env.VITE_APP_MAPBOX_TOKEN,
            map: {}
        };
    },
    mounted() {
        this.createMap();
    },

    watch: {
        newMarker(newV, oldV) {
            if (newV) this.addMarker();
        }
    },

    methods: {
        async createMap() {
            try {
                mapboxgl.accessToken = this.access_token;
                console.log(this.access_token);
                this.map = new mapboxgl.Map({
                    container: "map",
                    style: "mapbox://styles/mapbox/streets-v12",
                    center: this.coords,
                    zoom: 10
                });

                const that = this;
                this.map.on('click', function (e) {
                    const lngLat = [e.lngLat.lng, e.lngLat.lat];
                    that.$emit('NewMarkerCoords', lngLat);
                });

            } catch (err) {
                console.log("map error", err);
            }
        },

        addMarker() {
            console.log('addmarker');
            const elm = document.createElement('div');
            elm.className = 'marker';

            // Add Marker
            new mapboxgl.Marker({
                element: elm,
                anchor: 'bottom'
            }).setLngLat(this.newMarker.coords).addTo(this.map);

            // Add Popup
            new mapboxgl.Popup({ offset: 40 })
                .setLngLat(this.newMarker.coords)
                .setHTML(`<span class="popup border-${this.newMarker.type === 'running' ? 'green' : 'orange'}"><span> ${this.newMarker.title}.</span></span>`)
                .addTo(this.map);
        }
    },
}
</script> 

<style scoped>
.mapboxgl-popup-content {
    padding: 0 !important;
}
</style>