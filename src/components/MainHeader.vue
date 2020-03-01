<template>
    <header>
        <div id="headerTopBar"></div>
        <img id="headerImage" alt="Logo" src="../assets/food.svg">
    </header>
</template>

<script>
    
    export default {
        name: 'MainHeader',
    }

</script>

<style scoped>
    header {
        text-align: center;
    }

    #headerTopBar {
        width: 100vw;
        height: 1.5vw;
        background-image: url("../assets/background.svg");
        background-size: auto 100%;
        background-repeat: repeat-x;
    }

    #headerImage {
        margin-top: 3vw;
        width: 40vw;
    }

    @media only screen and (max-width: 850px) {
        #headerTopBar {
            height: 2.5vw;
        }

        #headerImage {
            margin-top: 5vw;
            width: 62.5vw;
        }
    }

    @media only screen and (max-width: 650px) {
        #headerTopBar {
            height: 3.5vw;
        }
        
        #headerImage {
            margin-top: 6.75vw;
            width: 65vw;
        }
    }

</style>
