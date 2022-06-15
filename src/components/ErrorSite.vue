<template>
  <section class="error">
        <h3>Ooops, an error occurred while loading.</h3>
        <p>The Pokémons went to sleep. Click on Pikachu to wake up the Pokémons.</p>
        <button @click="realoadData">
            <img src="@/assets/error.gif" alt="error" />
        </button>
  </section>
</template>

<script>
    export default {
        props: {
            realoadData: Function
        },

    }
</script>

<style>

    .error {
        text-align: center;
        height: 250px;
        padding: 20px 0;
    }

    .error h3 {
        margin-bottom: 20px;
    }

    .error img {
        max-height: 150px;
    }

    .error button {
        border: none;
    }

</style>