<template>
    <div class="grid w-full h-max  py-3 rounded bg-neutral-700">
        <div class="grid-auto-form lg:grid-rows-2 grid place-items-center gap-2">
            <label for="exerciseType" class="flex w-36 items-center justify-between">
                Type
                <select id="exerciseType" @change="switchSelect"
                    class="w-20 rounded py-0.5 px-1 text-neutral-800 outline-none">
                    <option value="running">Running</option>
                    <option value="cycling">Cycling</option>
                </select>
            </label>

            <label for="distance" class="flex w-36 items-center justify-between">
                Distance
                <input type="text" id="distance" v-model="data.distance" @keypress="isNumber($event)" maxlength="3"
                    placeholder="km"
                    class="w-20 rounded border-none py-0.5 px-2 text-neutral-800 outline-none focus:ring-0">
            </label>

            <label for="duration" class="flex w-36 items-center justify-between">
                Duration
                <input type="text" id="duration" v-model="data.duration" @keypress="isNumber($event)" maxlength="3"
                    placeholder="min"
                    class="w-20 rounded border-none py-0.5 px-2 text-neutral-800 outline-none focus:ring-0">
            </label>

            <label for="cadence" v-if="selectValue === 'running'" class="flex w-36 items-center justify-between">
                Cadence
                <input type="text" id="cadence" v-model="data.cadence" @keypress="isNumber($event)" maxlength="3"
                    placeholder="step/min" v-on:keyup.enter="submitForm"
                    class="w-20 rounded border-none py-0.5 px-2 text-neutral-800 outline-none focus:ring-0">
            </label>

            <label for="distance" v-if="selectValue === 'cycling'" class="flex w-36 items-center justify-between">
                Elev Gain
                <input type="text" id="distance" v-model="data.elevGain" @keypress="isNumber($event)" maxlength="3"
                    placeholder="m" v-on:keyup.enter="submitForm"
                    class="w-20 rounded border-none py-0.5 px-2 text-neutral-800 outline-none focus:ring-0">
            </label>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            selectValue: 'running',
            data: {
                distance: null,
                duration: null,
                cadence: null,
                elevGain: null
            }
        }
    },
    methods: {
        switchSelect(event) {
            this.selectValue = event.target.value;
        },
        submitForm() {
            let obj = { ...this.data };
            obj.type = this.selectValue;
            if (this.selectValue === 'running') delete obj.elevGain;
            if (this.selectValue === 'cycling') delete obj.cadence;
            let res = this.validate(obj);
            console.log('res', res);
            if (res == true) this.$emit('NewMarkerContent', obj);
            else this.$emit('NewMarkerContentError', res);
        },
        isNumber: function (evt) {
            evt = (evt) ? evt : window.event;
            let charCode = (evt.which) ? evt.which : evt.keyCode;
            if ((charCode > 31 && (charCode < 48 || charCode > 57)) && charCode !== 46) {
                evt.preventDefault();;
            } else {
                return true;
            }
        },
        validate(obj) {
            for (let i = 0; i < Object.values(obj).length; i++) {
                let e = Object.values(obj)[i];
                if (!e) return 'Please provide all fields with proper values.';
                if (e <= 0) return 'Please enter non-zero positive numbers only.';
            }
            return true;

        }
    }
}
</script>