<template>
    <div class="container" id="home-container">
        <div class="columns">
            <SolanaComponent></SolanaComponent>
        </div>
    </div>
</template>

<script>
    // import HomeComponent from "../components/home.vue";
    import SolanaComponent from "../components/solana.vue";
    export default {
        name: 'home',
        components: {
            // HomeComponent,
            SolanaComponent,
        }
    }
</script>