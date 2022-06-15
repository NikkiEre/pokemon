<template>
  <section class="notfound">
        <h3>Unfortunately, this Pokemon could not be caught.</h3>
        <p>He broke free and ran away. <span @click="realoadData">Click here</span> to download a fresh Pokémon.</p>
        <img src="@/assets/run.gif" alt="run" />
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
    .notfound {
        text-align: center;
        height: 250px;
        padding: 20px 0;
        overflow-x: hidden;
    }

    .notfound h3 {
        margin-bottom: 20px;
    }

    .notfound p span {
        padding: 10px;
        background-color: goldenrod;
        color: #fff;
        transition: all 0.5s;
        cursor: pointer;
    }

    .notfound p span:hover {
        background: lightgray;
    }

    .notfound img {
        max-height: 150px;
        float: left;
        animation: run 5s linear infinite;
    }

    @keyframes run {
        from {
            transform: translateX(-200px);
        }
        to {
            transform: translateX(calc(1200px + 200px));
        }
    }

</style>