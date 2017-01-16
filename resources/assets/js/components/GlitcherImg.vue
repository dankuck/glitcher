<template>
    <img 
        :src="base64Data" 
        :style="{'max-width': (maxWidth || 300) + 'px', 'max-height': (maxHeight || 300) + 'px'}"
        @click="clicked"
        :class="this.class"
    />
</template>

<script>
export default {
    props: ['data', 'maxWidth', 'maxHeight', 'class'],
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
