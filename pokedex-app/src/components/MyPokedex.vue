<template>
    <v-row justify="end">
        <v-col cols="3">
            <audio controls loop>
                <source src="../assets/sounds/background-song.wav" type="audio/wav">
            </audio>
        </v-col>
    </v-row>
    <div class="container">
        <div class="poke-detail">
            <PokemonDetail v-if="showDetail" :pokemonUrl="pokemonUrl"></PokemonDetail>
        </div>
        <div class="poke-list">
            <PokemonList :apiUrl="apiUrl" @closeDetail="closeDetail" @setPokemonUrl="setPokemonUrl"></PokemonList>
        </div>
        <img class="bg-pokedex" src="../../src/assets/pokedex.png">
    </div>
</template>

<script>
import PokemonList from './PokemonList.vue';
import PokemonDetail from './PokemonDetail.vue';

export default {
    data() {
        return {
            apiUrl: 'https://pokeapi.co/api/v2/pokemon',
            pokemonUrl: '',
            showDetail: false
        }
    },
    components: {
        PokemonList,
        PokemonDetail
    },
    methods: {
        setPokemonUrl(url) {
            this.pokemonUrl = url;
            this.showDetail = true;
        },
        closeDetail() {
            this.pokemonUrl = '';
            this.showDetail = false;
        }
    }
}
</script>

<style lang="scss" scoped>
.bg-pokedex {
    max-width: 100%;
    max-height: 100%;
}

.container {
    display: flex;
    flex-flow: row wrap;
    justify-content: center;
    align-items: center;
}

.poke-list {
    position: absolute;
    margin-left: 25%;
    margin: 4px, 4px;
    padding: 4px;
    width: 280px;
    height: 380px;
    overflow-x: hidden;
    overflow-y: auto;
    text-align: justify;

    /* Hide scrollbar for IE, Edge and Firefox */
    -ms-overflow-style: none;
    /* IE and Edge */
    scrollbar-width: none;
    /* Firefox */
}

.poke-list::-webkit-scrollbar {
    /* Hide scrollbar for Chrome, Safari and Opera */
    display: none;
}

.poke-detail {
    position: absolute;
    margin-right: 20%;
    margin-top: 5%;
    //  margin:4px, 4px;
    //  padding:4px;
    width: 375px;
    height: 540px;
    border-radius: 5px;
    overflow-x: hidden;
    overflow-y: auto;

    /* Hide scrollbar for IE, Edge and Firefox */
    -ms-overflow-style: none;
    /* IE and Edge */
    scrollbar-width: none;
    /* Firefox */
}

.poke-detail::-webkit-scrollbar {
    /* Hide scrollbar for Chrome, Safari and Opera */
    display: none;
}
</style>
