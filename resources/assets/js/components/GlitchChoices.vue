<template>
    <div>
        <glitcher-img v-for="(data, index) in mutations" :data="data" @click="drill(index)">
        </glitcher-img>
        <glitch-choices v-if="chosen" :data="chosen" :mutators="mutators"></glitch-choices>
    </div>
</template>

<script>
export default {
    props: ['data', 'mutators'],
    data() {
        return {
            mutations: [],
            chosen: null,
        };
    },
    mounted() {
        Vue.nextTick(() => {
            this.mutations = this.mutators.map((mutator) => mutator(this.data));
        });
    },
    methods: {
        drill(index) {
            this.chosen = null;
            // Give things a chance to clean up before reprocessing
            Vue.nextTick(() => this.chosen = this.mutations[index]);
        },
    },
}
</script>
