<template>
    <div>
        <input type="file" @change="openFile" />
        <div v-if="originalData">
            <glitcher-img :data="originalData"></glitcher-img>
        </div>
        <glitch-choices v-if="originalData" :data="originalData"></glitch-choices>
    </div>
</template>

<script>
export default {
    data() {
        return {
            fileInput: null,
            originalData: null,
        };
    },
    methods: {
        openFile(e) {
            this.readBlob(e.target.files[0]);
        },
        readBlob(blob) {
          var reader = new FileReader();
          reader.onload = (read) => {
            this.originalData = read.target.result;
          };
          reader.readAsBinaryString(blob);
        },
    }
}
</script>
