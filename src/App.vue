<template>
    <div id="app">
        <WebUrlParser/>
    </div>
</template>

<script>
    import WebUrlParser from './components/WebUrlParser.vue'

    export default {
        name: 'app',
        components: {
            WebUrlParser
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c2c2c;
        background: #eeeeee;
        width: 100vw;
        height: 100vh;
        padding-top: 29px;
    }
</style>
