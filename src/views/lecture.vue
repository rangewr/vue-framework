<template>
    <div>
        <input v-model="input1">
        <p>P标签</p>
        <p v-html="input2">
            {{input1}}
        </p>
        <p>
            {{5+5}}<br>
            {{ ok ? 'YES' : 'NO' }}<br>
            {{ message.split('').reverse().join('') }}

        </p>

        <div v-bind:class="{'class1': use}">
            v-bind:class 指令
        </div>

        <div v-if="divBool">
            divBool
        </div>
        <input type="button" value="原button" v-on:click="button1">
        <input type="button" value="缩写button" @click="button1">
        <a v-bind:href="input3">原格式</a>
        <a :href="input3">缩写格式</a>
        <ul>
            <li v-for="site in sites">
                {{ site.name }}----age == {{site.age}}
            </li>

        </ul>
        <hr>
        <ul>
            <li v-for="(value, key, index) in object">
                [{{index}}]{{key}}--:--{{ value }}
            </li>
        </ul>
        <!--<ul>-->
            <!--<li v-for="n in 10">-->
                <!--{{ n }}-->
            <!--</li>-->

        <!--</ul>-->
    </div>
</template>

<script>
    export default {
        data() {
            return {
                input1: "",
                input2: "<h1>啊啊啊</h1>",
                input3: "http://www.baidu.com",
                input4: "",
                input5: "",
                use: true,
                message: "abcdefg",
                ok: true,
                divBool: true,
                sites: [
                    { name: 'Runoob',age:'12' },
                    { name: 'Google',age:'14' },
                    { name: 'Taobao' ,age:'16'}
                ],
                object: {
                    name: '百度',
                    url: 'http://www.baidu.com',
                    slogan: '百度一下,你就知道！'
                },

            }
        },
        methods: {
            button1() {
                alert("button1");
            }
        }
    }
</script>

<style scoped>
    .class1 {
        color: red;
    }
</style>