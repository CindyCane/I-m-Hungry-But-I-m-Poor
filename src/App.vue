<template>
    <div id="app">
        <MainHeader/>
        <BodyMap/>
        <MainFooter/>
    </div>
</template>

<script>
    import MainHeader from './components/MainHeader.vue'
    import BodyMap from './components/BodyMap.vue'
    import MainFooter from './components/MainFooter.vue'

    export default {
        name: 'app',
        components: {
            MainHeader,
            BodyMap,
            MainFooter
        }
    }

</script>

<style>
    @import url('https://fonts.googleapis.com/css?family=Open+Sans:400,700,800&display=swap');
    @import url('https://fonts.googleapis.com/css?family=Aleo:400,700&display=swap');
    
    body, html {
        margin: 0;
        padding: 0;
    }
</style>
