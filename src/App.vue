<template>
  <div>
    <PokeCard />
    <div v-for="pokemon in pokemones" :key="pokemon.name">
      <img :src="pokemon.image" :alt="pokemon.name" />
      <p>{{ pokemon.name }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import PokeCard from './components/PokeCard.vue';

export default {
  name: 'App',
  components: {
    PokeCard,
  },
  data() {
    return {
      pokemones: []
    };
  },
  async created() {
    const pokemones = await this.getPokemons();
    this.pokemones = pokemones;
  },
  methods: {
    async getPokemons() {
      const URL_BASE = "https://pokeapi.co/api/v2/pokemon";
      try {
        const responseApi = await axios.get(URL_BASE);
        const primerLlamado = responseApi.data.results;

        const pokemonResponse = await Promise.all(
          primerLlamado.map(async (pokemon) => {
            const { data } = await axios.get(pokemon.url);
            return { name: data.name, image: data.sprites.front_default };
          })
        );

        return pokemonResponse;
      } catch (error) {
        console.error("Error fetching Pokémon data: ", error);
        return [];
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
