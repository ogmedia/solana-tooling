<template>
    <HomeContainer></HomeContainer>
</template>
<script>
    import HomeContainer from "./containers/home.vue";
    export default {
        mounted(){
            this.$store.dispatch("finishedLoading");
            console.log(this.$store);
        },
        components: {
            HomeContainer
        }
    }
</script>