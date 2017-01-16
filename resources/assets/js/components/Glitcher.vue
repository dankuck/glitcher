<template>
    <div>
        <input type="file" @change="openFile" />
        <div style="width: 100%; height: 330px; overflow-x: scroll">
            <glitcher-img v-for="(data, index) in earlier" :data="data" @click="rechoose(index)">
            </glitcher-img>
        </div>
        <glitcher-img v-if="current" :data="current" :maxWidth="500" :maxHeight="500">
        </glitcher-img>
        <div>
            <glitcher-img v-for="(data, index) in mutations" :data="data" @click="choose(index)">
            </glitcher-img>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            fileInput: null,
            path: [],
            mutations: [],
            mutators: [
                function (data) {
                  var x = parseInt(Math.random() * data.length);
                  var y = parseInt(Math.random() * data.length);
                  return data.substr(0, x) + data[y] + data.substr(x + 1, y - x) + data[x] + data.substr(y + 1);
                },
                function (data) {
                  var i = parseInt(Math.random() * data.length);
                  return data.substr(0, i) + "\0" + data.substr(i + 1);
                },
                function (data) {
                  var i = parseInt(Math.random() * data.length);
                  return data.substr(0, i) + String.fromCharCode(Math.random() * 256) + data.substr(i + 1);
                },
                function (data) {
                  var i = parseInt(Math.random() * data.length);
                  var length = Math.random() * 64;
                  return data.substr(0, i) + data.substr(i, length).split("").reverse().join("") + data.substr(i + length);
                },
            ],
        };
    },
    computed: {
        current() {
            return this.path[this.path.length - 1];
        },
        earlier() {
            return this.path.slice(0, -1);
        },
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
                this.path = [read.target.result];
                this.remutate();
            };
            reader.readAsBinaryString(blob);
        },
        choose(index) {
            this.path.push(this.mutations[index]);
            this.remutate();
        },
        rechoose(index) {
            this.path.splice(index + 1);
            this.remutate();
        },
        remutate() {
            var data = this.current;
            this.mutations = this.mutators.map((mutator) => mutator(data));
        },
    },
}
</script>
