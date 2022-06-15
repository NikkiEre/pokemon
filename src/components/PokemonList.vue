<template>
  <ul class="pokemon-list">
    <li 
        class="pokemon-list-item" 
        v-for="pokemon in pokemon_list" 
        :key="pokemon.name"
    >
        <PokemonItem 
            :pokemon="pokemon"
            :fetchDescriptionPokemon="fetchDescriptionPokemon"
        />
    </li>
  </ul>
</template>

<script>
    import PokemonItem from './PokemonItem.vue';

    export default {
        components: {
            PokemonItem,
        },
        props: {
            pokemon_list: Array
        },
        methods: {
            fetchDescriptionPokemon: function(url) {            // Функция загрузки описания покемона
                const description = fetch(url)
                    .then(response => response.json());
                return description;
            },
        },
    }
</script>

<style>
    .pokemon-list {
        list-style-type: none;
    }

    .pokemon-list-item {
        border: 1px solid goldenrod;
        text-align: left;
    }

    .pokemon-list-item:not(:last-child) {
        border-bottom: none;
    }

</style>