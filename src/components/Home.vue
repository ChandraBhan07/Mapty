<script setup>
import Form from './Form.vue';
import Map from './Map.vue';
import EventsContainer from './EventsContainer.vue';
const emit = defineEmits(['NewMarkerCoords']);
</script>

<template>
    <span v-if="alert.isOpen" class="flex justify-center text-white">
        <span class="rounded-b-lg p-2 px-14 absolute z-10"
            :class="alert.status == 'success' ? 'bg-green-500' : 'bg-red-500'">
            {{ alert.text }}
        </span>
    </span>
    <div class="grid grid-cols-1 sm:grid-cols-3">
        <div class="p-4 pr-0">
            <div
                class="flex h-full rounded-tl-md rounded-bl-md flex-col items-center justify-between bg-neutral-800 p-5">
                <!-- logo -->
                <div><img src="../assets/logo.png" class="w-36"></div>

                <div class="flex flex-col w-full h-full items-center mt-6 gap-3  text-sm text-white">
                    <Form v-if="newMarkerCoords.length" v-on:NewMarkerContent="setNewMarker"
                        v-on:NewMarkerContentError="setNewMarkerError" />
                    <EventsContainer :eventsArr="eventsArr" />
                </div>

                <div class="text-white">© Copyright by Chandrabhan Singh Parihar</div>
            </div>
        </div>

        <div class="col-span-2 min-h-screen">
            <div v-if="coords.length" class="p-4 pl-0 w-full h-full rounded-tr-md rounded-br-md">
                <!-- map -->
                <Map :coords="coords" v-on:NewMarkerCoords="setNewMarkerCoords" :newMarker="newMarker" />
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            coords: [],
            newMarkerCoords: [],
            newMarker: {},
            eventsArr: [],
            alert: {
                isOpen: false,
                text: 'Click on the map to start adding markers.',
                // status success or error
                status: 'success'
            }

        }
    },
    watch: {
        alert(n, o) {
            console.log(n);
        },
        deep: true
    },

    created() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                (position) => {
                    const { latitude, longitude } = position.coords;
                    // mapbox needs lng, lat
                    this.coords = [longitude, latitude];
                },
                () => {
                    alert('Could not get your location.')
                }
            );
        }
        setTimeout(() => {
            this.alert.isOpen = true;
        }, 1500);
    },

    methods: {
        setNewMarkerCoords(coords) {
            //format lnglat
            this.newMarkerCoords = coords;
            this.alert.isOpen = true;
            this.alert.text = 'Now fill the form and press enter';
        },

        setNewMarker(content) {
            content.coords = this.newMarkerCoords;
            // date, in future it can come from other modules
            const options = { day: 'numeric', month: 'short' };
            const date = (new Date()).toLocaleDateString('en-IN', options);
            content.title = `${content.type === 'running' ? 'Running' : 'Cycling'} on ${date}`;
            this.newMarker = content;
            this.renderEvents(content);
        },

        setNewMarkerError(error) {
            this.alert.isOpen = true;
            this.alert.text = error;
            this.alert.status = 'error';
        },

        renderEvents(content) {
            this.eventsArr.push(content);
            this.alert.status = 'success';
            this.alert.text = 'Okay now its upto you, Cheers 😎';
            setTimeout(() => {
                this.alert.isOpen = false;
            }, 3500);
            this.newMarkerCoords = [];
        }
    }

}
</script>
