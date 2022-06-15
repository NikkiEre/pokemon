<template>

  <main class="app">
    <h1 class="app-title">Pokemon site</h1>

    <!-- Поиск покемонов -->

    <SearchSite 
      :text=search
      :changeSearchText="changeSearchText"
      :fetchSearchPokemon="fetchSearchPokemon"
    />

    <!-- Список покемонов -->

    <PokemonList
      v-if="!status.isError && !status.isLoad && !notFound && pokemon.length"
      :pokemon_list="pokemon"
    />

    <!-- Ошибка при загрузки данных -->

    <ErrorSite 
      v-else-if="status.isError && !status.isLoad" 
      :realoadData="getPokemons"  
    />

    <!-- Загрузка данных -->

    <LoaderSite  v-else-if="status.isLoad && !status.isError"/>

    <!-- Данные не найдены -->

    <NotFoundSite 
      v-else-if="!status.isLoad && !status.isError && notFound" 
      :realoadData="getPokemons" 
      
    />

    <!-- Пагинация -->

    <PaginationSite 
      v-if="!status.isError && !status.isLoad && !notFound && pokemon.length === countPokemonsOnPage" 
      :page="page" 
      :changeNumberPage="changeNumberPage" 
    />
  </main>

</template>

<script>
  import PaginationSite from './components/PaginationSite.vue';
  import PokemonList from './components/PokemonList.vue';
  import ErrorSite from './components/ErrorSite.vue';
  import LoaderSite from './components/LoaderSite.vue';
  import SearchSite from './components/SearchSite.vue';
  import NotFoundSite from './components/NotFoundSite.vue';

  export default {
    name: 'App',
    components: {
      PaginationSite,
      PokemonList,
      ErrorSite,
      LoaderSite,
      SearchSite,
      NotFoundSite,
    },
    data: function() {
      return {
        pokemon_list: [],                                                 // Список покемонов
        number_page: 1,                                                   // Текущий номер страницы
        countPokemonsOnPage: 20,                                          // Количество элементов на странице
        status_pokemon_list: {                                            // Статус списка покемонов
          isError: false,                                                 // Произошла ошибка при загрузке данных
          isLoad: false,                                                  // Происходит загрузка данных
        },
        search_text: '',                                                  // Текст в поисковой строке
        isNotFound: false,                                                // Статус поиска покемона
      }
    },
    computed: {
      pokemon: {
        get: function() {                                                 // Геттер списка покемонов
          return this.pokemon_list;
        },
        set: function(arr) {                                              // Сеттер списка покемонов
          this.pokemon_list = [...arr];
          sessionStorage.setItem('pokemon', JSON.stringify(this.pokemon));  // Сохраняем список покемонов в сессию
        }
      },
      page: {
        get: function() {                                                 // Геттер номера страницы
          return this.number_page;
        },
        set: function(number) {                                           // Сеттер номера страницы
          this.number_page = number;
          this.getPokemons();
        }
      },
      status: {
        get: function() {                                                 // Геттер статуса списка покемонов
          return this.status_pokemon_list;
        },
        set: function({field, status}) {                                  // Сеттер статуса списка покемонов
          this.status_pokemon_list[field] = status;
        },
      },
      search: {
        get: function() {                                                 // Геттер значения поисковой строки
          return this.search_text;
        },
        set: function(text) {                                             // Сеттер значения поисковой строки
          this.search_text = text;
        },
      },
      notFound: {
        get: function() {                                                // Геттер статуса поиска
          return this.isNotFound;
        },
        set: function(status) {                                          // Сеттер статуса поиска
          this.isNotFound = status;
        }
      },
    },
    methods: {
      setStatusError: function(status) {                                  // Функция изменения статуса ошибки
        this.status = {field: 'isError', status: status};
      },
      setStatusLoad: function(status) {                                   // Функция изменения статуса загрузки
        this.status = {field: 'isLoad', status: status};
      },
      getPokemons: async function () {                                    // Функция получения списка покемонов в зависемости от страницы
        this.setStatusError(false);                                       // Обнуляем статус ошибки
        this.notFound = false;                                            // Обнуляем статус поиска
        this.setStatusLoad(true);                                         // Указываем статус загрузки
        await fetch(`https://pokeapi.co/api/v2/pokemon/?limit=${this.countPokemonsOnPage}&offset=${this.countPokemonsOnPage * this.page}`)
          .then(response => response.json())
          .then(data => this.pokemon = data.results)
          .catch(() => this.setStatusError(true))
          .finally(() => this.setStatusLoad(false));                      // Обнуляем статус загрузки
      },
      changeNumberPage: function(e) {                                     // Функция изменения страницы
        if(e.target.getAttribute('btn_type') === 'next' && this.page < 55) {
          this.page += 1;
        } else if (e.target.getAttribute('btn_type') === 'back' && this.page > 1) {
          this.page -= 1;
        }
        sessionStorage.setItem('number_page', JSON.stringify(this.page)); // Сохраняем номер страницы в сессию
      },
      changeSearchText: function(e) {                                     // Функция изменения поисковой строки
        this.search = e.target.value;
        if(!this.search) {
          this.getPokemons();
        }
        sessionStorage.setItem('search_text', JSON.stringify(this.search)); // Сохраняем значение поисковой строки в сессию
      },
      fetchSearchPokemon: async function () {                             // Функция запроса на поиск определенного покемона
        if(!this.search.trim()) return;                                   // Если строка пустая запроса не будет
        this.setStatusError(false);                                       // Обнуляем статус ошибки
        this.notFound = false;                                            // Обнуляем статус поиска
        this.setStatusLoad(true);                                         // Устанавливаем статус загрузки
        await fetch(`https://pokeapi.co/api/v2/pokemon/${this.search}`)
          .then(async res => {
            if(res.ok) {                                                  // Если нашли покемона, то добавляем его в список
              const result = await res.json();
              this.pokemon = [result];
            } else {                                                      // Если не нашли покемона, то проверяем что его нет. Если ошибка с подключение то сеттаем ошибку подключения
              res.status >= 400 && res.status < 500
                ? this.notFound = true
                : this.setStatusError(true);
            }
          })
          .catch(() => this.setStatusError(true))                         // Если ошибка с подключение то сеттаем ошибку подключения            
          .finally(() => this.setStatusLoad(false));                      // Обнуляем статус загрузки                      
      },
    },
    mounted: function() {
      if(sessionStorage.getItem('pokemon')) {                            // Проверяем есть в сессии сохраненные данные о списке покемонов, если есть то добавляем их иначе загружаем
        this.pokemon = JSON.parse(sessionStorage.getItem('pokemon'));
      } else {
        this.getPokemons();
      }
    },
    created: function() {
      if(sessionStorage.getItem('number_page')) {                        // Проверяем есть в сессии сохраненные данные о номере страницы, если есть то добавляем их    
        this.page = JSON.parse(sessionStorage.getItem('number_page'));
      }
      if(sessionStorage.getItem('search_text')) {                        // Проверяем есть в сессии сохраненные данные о значении поисковой страницы, если есть то добавляем их
        this.search = JSON.parse(sessionStorage.getItem('search_text'));
      }
    },
  }
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  .app {
    max-width: 1300px;
    margin: auto;
    padding: 0 15px;
  }

  .app-title {
    margin-bottom: 30px;
  }
</style>
