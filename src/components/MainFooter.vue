<template>
    <Footer>
        <div id="footerTopBar"></div>
    </Footer>
</template>

<script>
    
    export default {
        name: 'MainFooter',
    }

</script>

<style scoped>
    footer {
        text-align: center;
    }

    #footerTopBar {
        width: 100vw;
        height: 1.5vw;
        background-image: url("../assets/background.svg");
        background-size: auto 100%;
        background-repeat: repeat-x;
    }

    @media only screen and (max-width: 850px) {
        #footerTopBar {
            height: 2.5vw;
        }
    }

    @media only screen and (max-width: 650px) {
        #footerTopBar {
            height: 3.5vw;
        }
    }

</style>
