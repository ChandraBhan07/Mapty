<template>
    <div v-if="eventsArr.length" v-for="(event, index) in eventsArr" :key="index"
        class="mt-2 grid h-max w-full rounded border-l-4 bg-neutral-700 py-3"
        :class="event.type === 'running' ? 'border-green' : 'border-orange'">

        <div class="mb-2 pl-10 sm:pl-5">{{ event.title }}</div>
        <div class="grid-auto-event w-full grid place-items-center gap-2">
            <div>{{ event.type === 'running' ? '🏃‍♂️' : '🚴‍♂️' }} {{ event.distance }} <span
                    class="text-[0.65rem]">KM</span></div>
            <div>⌚ {{ event.duration }} <span class="text-[0.65rem]">MIN</span></div>
            <div>⚡ {{ event.distance > 0 ? Math.round(event.distance * 60 / event.duration) : 0 }} <span
                    class="text-[0.65rem]">KM/H</span></div>
            <div v-if="event.type === 'running'">🦶 2 <span class="text-[0.65rem]">SPM</span></div>
            <div v-else>
                <div>🚲 2 <span class="text-[0.65rem]">M</span></div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        eventsArr: {
            type: Array,
            required: true
        }
    }
}
</script>