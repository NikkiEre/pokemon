<template>
  <form @submit.prevent="" class="search">
    <input 
        type="text" 
        :value="text" 
        :onchange="changeSearchText" 
        placeholder="Enter name pokemon" 
    />
    <button @click="fetchSearchPokemon">
        Search Pokemon 
        <img src="@/assets/pokeball.svg" alt='pokeball' />
    </button>
  </form>
</template>

<script>
    export default {
        props: {
            text: String,
            changeSearchText: Function,
            fetchSearchPokemon: Function,
        },
    }
</script>

<style>

    .search {
        display: flex;
        justify-content: space-between;
        margin: 20px 0px;
    }

    .search input {
        flex: 3;
        padding: 20px 10px;
        border-right: none;
        font-size: 16px;
        position: relative;
    }

    .search button {
        display: flex;
        justify-content: center;
        flex: 1;
        padding: 20px 10px;
        border-left: none;
        font-size: 16px;
        background-color: goldenrod;
        color: #fff;
        cursor: pointer;
        transition: all 0.5s;
    }

    .search button img{
        height: 20px;
        margin-left: -10px;
        opacity: 0;
        transition: all cubic-bezier(0.22, 0.61, 0.36, 1) 1s;
    }


    .search button:hover {
        background-color: lightgray;
    }

    .search button:hover img{
        opacity: 1;
        transform: translateX(50px) rotate(800deg);
    }

</style>