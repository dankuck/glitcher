<template>
    <img 
        :src="base64Data" 
        style="max-width: 300px; max-height: 300px;"
        @click="clicked"
    />
</template>

<script>
export default {
    props: ['data'],
    computed: {
        base64Data() {
            return "data:image/jpg;base64," + btoa(this.data);
        },
    },
    methods: {
        clicked(e) {
            this.$emit('click', e);
        }
    },
}
</script>
