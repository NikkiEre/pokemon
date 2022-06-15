<template>
  <section class="loader">
        <h3>Wait, we're loading Pokémon.</h3>
        <p>It will take some time.</p>
        <img src="@/assets/load.gif" alt="loader" />
    </section>
</template>

<script>
    export default {

    }
</script>

<style>
    .loader {
        text-align: center;
        height: 250px;
        padding: 20px 0;
    }

    .loader h3 {
        margin-bottom: 20px;
    }

    .loader img {
        max-height: 150px;
    }

</style>