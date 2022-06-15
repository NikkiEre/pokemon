<template>
  <details class="pokemon-item" @click="getDescriptionPokemon">
    <summary>
        {{ pokemon.name }}
        <img src="@/assets/pokeball.svg" alt="pokeball" />
    </summary>

    <!--Данные о покемоне. Показываются если нет ошибок загрузки, самой загрузки и если данные есть-->

    <section 
        class="pokemon-item-content" 
        v-if="!pokemon_status.isLoad && !pokemon_status.isError && pokemon_description"
    >
        <h6 class="pokemon-item-title">Description Pokemon</h6>
        <div class="pokemon-item-params">
            <p>Name: <b>{{ pokemon_description.name }}</b></p>
            <p>Id: <b>{{ pokemon_description.id }}</b></p>
            <p>Height: <b>{{ pokemon_description.height }}</b></p>
            <p>Weight: <b>{{ pokemon_description.weight }}</b></p>
        </div>

        <img :src="pokemon_description.sprites.other.home.front_default" />
    </section>

    <!--Загрузка данных о покемоне-->

    <LoaderSite 
        v-else-if="pokemon_status.isLoad && !pokemon_status.isError"
    />

    <!--Ошибка загрузки данных-->

    <ErrorSite 
        v-else-if="pokemon_status.isError && !pokemon_status.isLoad" 
        :realoadData="getDescriptionPokemon" 
    />
  </details>
</template>

<script>
    import LoaderSite from "./LoaderSite.vue";
    import ErrorSite from "./ErrorSite.vue";

    export default {
        components: {
            LoaderSite,
            ErrorSite,
        },
        props: {
            pokemon: Object,
            fetchDescriptionPokemon: Function
        },
        data: function () {
            return {
                description: null,
                statusItem: {
                    isError: false,
                    isLoad: false,
                }
            };
        },
        computed: {
            pokemon_description: {
                get: function () {                                             // Геттер описания покемона
                    return this.description;
                },
                set: function (description) {                                  // Сеттер описания покемона
                    this.description = description;
                }
            },
            pokemon_status: {
                get: function() {                                              // Геттер статуса загрузки описания покемона
                    return this.statusItem;
                },
                set: function({field, status}) {                               // Сеттер статуса загрузки описания покемона
                    this.statusItem[field] = status;
                },
            }
        },
        methods: {
            setStatusLoad: function(isStatus) {                                // Функция изменения статуса загрузки описания покемона
                this.pokemon_status = {field: 'isLoad', status: isStatus};
            },
            setStatusError: function(isStatus) {                               // Функция изменения статуса ошибки описания покемона
                this.pokemon_status= {field: 'isError', status: isStatus};
            },
            getDescriptionPokemon: async function () {                         // Функция загрузки описания покемона
                if(this.pokemon_description) return;                           // Если описание покемона есть, то не загружаем данные
                this.setStatusError(false);                                    // Обнуляем статус ошибки
                this.setStatusLoad(true);                                      // Устанавливаем статус загрузки
                await fetch(this.pokemon.url)
                    .then(response => response.json())
                    .then(description => this.pokemon_description = description)
                    .catch(() => this.setStatusError(true))                    // Устанавливаем статус ошибки    
                    .finally(() => this.setStatusLoad(false))                  // Обнуляем статус загрузки
            },
        },
        mounted: function() {
            if(Object.prototype.hasOwnProperty.call(this.pokemon, "height")) { // Если у нас есть полное описание покемона из поиска, то добавляем его в описание
                this.pokemon_description = {...this.pokemon};
            }
        }
    }
</script>

<style>

    .pokemon-item summary {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 20px 15px;
        cursor: pointer;
        transition: all 0.5s;
        text-transform: capitalize;
    }

    .pokemon-item summary::marker {
        display: none;
    }

    .pokemon-item:hover summary {
        color: #fff;
        background-color: goldenrod;
    }

    .pokemon-item summary img{
        height: 30px;
        transition: all 0.5s;
    }

    .pokemon-item[open] summary {
        background-color: goldenrod;
        color: #fff;
    }

    .pokemon-item[open] summary img {
        height: 30px;
        transform: rotate(45deg);
    }

    .pokemon-item-content {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        padding: 20px 15px;
        background: #fff;
    }

    .pokemon-item-title {
        font-size: 20px;
        margin-bottom: 20px;
        width: 100%;
    }

    .pokemon-item-params {
        float: left;
        height: fit-content;
        align-self: center;
        text-transform: capitalize;
    }

    .pokemon-item-params p:not(:last-child) {
        margin-bottom: 20px;
    }

    .pokemon-item-content img {
        max-height: 200px;
    }

</style>