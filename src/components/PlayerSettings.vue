<template>
<div>
    <v-card outlined elevation="2">
            <v-select v-model="waveType" name="waveType" id="waveType" label="waveType" :items="waveTypeOptions" @input="onSetWaveType"></v-select>
    </v-card>
</div>
</template>

<script>
export default {
    data() {
        return {
            transposeAmount: 0, //0 corresponds to the key of C
            waveType: 'sine',
            waveTypeOptions: ['sine', 'triangle', 'square', 'sawtooth']
        }
    },
    methods: {
        onSetWaveType(val) {
            this.$emit('setWaveType', val);
        }
    }
}
</script>

<style scoped>
</style>