<template>
    <div>
        <input type="file" @change="openFile" />
        <div class="history">
            HISTORY
            <transition-group name="fade">
                <glitcher-img v-for="(data, index) in history" :key="data" class="pointer" :data="data" @click="rechoose(index)" :maxHeight="100" :maxWidth="100">
                </glitcher-img>
            </transition-group>
            <div v-if="history.length === 0" class="explainer">
                Open or paste an image, then choose a mutation to get started.
            </div>
        </div>
        <div class="work">
            <div class="current" v-if="current">
                <glitcher-img :data="current">
                </glitcher-img>
            </div>
            <transition-group name="fade">
                <div class="mutation pointer" v-for="(data, index) in mutations" :key="data">
                    <glitcher-img :data="data" @click="choose(index)">
                    </glitcher-img>
                </div>
            </transition-group>
        </div>
        <div style="clear: both"></div>
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
                function (data) {
                  var x = parseInt(Math.random() * data.length);
                  var y = parseInt(Math.random() * data.length);
                  return data.substr(0, x) + (data[y] ^ data[x]) + data.substr(x + 1, y - x) + (data[y] ^ data[x]) + data.substr(y + 1);
                },
            ],
        };
    },
    mounted() {
        Vue.nextTick(() => {
            document.addEventListener('paste', (event) => {
                for (var i = 0; i < event.clipboardData.items.length; i++) {
                    if (/image\//.test(event.clipboardData.items[i].type)) {
                        this.convertToJpg(event.clipboardData.items[i].getAsFile());
                        return;
                    }
                }
            });
        });
    },
    computed: {
        current() {
            return this.path[this.path.length - 1];
        },
        history() {
            return this.path.slice(0, -1).reverse();
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
        convertToJpg(blob) {
            var reader = new FileReader();
            reader.onload = (read) => {
                var str = read.target.result;
                var canvas = document.createElement('canvas');
                document.body.appendChild(canvas);
                var context = canvas.getContext('2d');
                var img = new Image();
                img.src = "data:image/png;base64," + btoa(str);
                img.onload = () => {
                    canvas.width = img.width;
                    canvas.height = img.height;
                    context.drawImage(img, 0, 0);
                    canvas.toBlob((blob) => {
                        this.readBlob(blob);
                        document.body.removeChild(canvas);
                    }, 'image/jpeg', 11/12);
                };
            };
            reader.readAsBinaryString(blob);
        },
        choose(index) {
            this.path.push(this.mutations[index]);
            this.remutate();
        },
        rechoose(index) {
            this.path.splice(this.path.length - (index + 1));
            this.remutate();
        },
        remutate() {
            this.mutations = [];
            var data = this.current;
            var mutators = this.mutators.slice();
            var gen = () => {
                this.mutations.push(mutators.pop()(data));
                if (mutators.length) {
                    setTimeout(gen, 100);
                }
            };
            setTimeout(gen, 100);
        },
    },
}
</script>

<style>
.history {
    width: 120px;
    float: left;
    min-height: 1800px;
    margin: 1em;
}
    .history img {
        padding: 10px;
        border-radius: 15px;
    }
.work {
    margin: 1em;
}
.current, .mutation {
    float: left;
    margin: 1em;
}
    .current img, .mutation img {
        border-radius: 15px;
        border: 3px solid #DDD;
    }
    .current img {
        border-color: #844;
    }
.explainer {
    font-size: 90%;
    font-style: italic;
    margin: .5em;
}
.fade-enter-active, .fade-leave-active {
    transition: all .3s;
}
.fade-enter, .fade-leave-to {
    opacity: 0;
}
.pointer {
    cursor: pointer;
}
</style>
