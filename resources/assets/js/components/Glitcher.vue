<template>
    <div>
        <input type="file" @change="openFile" />
        <div v-if="originalData">
            <glitcher-img :data="originalData"></glitcher-img>
        </div>
        <glitch-choices v-if="originalData" :data="originalData" :mutators="mutators"></glitch-choices>
    </div>
</template>

<script>
export default {
    data() {
        return {
            fileInput: null,
            originalData: null,
            mutators: [
                function (data) {
                  var x = parseInt(Math.random() * data.length);
                  var y = parseInt(Math.random() * data.length);
                  return data.substr(0, x) + data[y] + data.substr(x + 1, y - x) + data[x] + data.substr(y + 1);
                },
            ],
        };
    },
    methods: {
        openFile(e) {
            this.originalData = null;
            // give things a chance to clean up before reading in the files
            Vue.nextTick(() => this.readBlob(e.target.files[0]));
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
