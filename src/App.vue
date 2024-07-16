<template>
  <div>
    <img src="./assets/ED7FFF21-A4F8-463B-B446-B93DD3FE2DA6.png" alt="pokemon">
    <h1>¿Quién es ese Pokemón?</h1>
    <h5>Pokémons atrapados:<img class="poke-bola" v-for=" n in countGuessed " :key="n" src="../public/favicon.png"
        alt=""></h5>
  
    <div class="container">
      <div class="row row-cols-1 row-cols-md-3 row-cols-lg-4">

        <div class="col" v-for="(pokemon) in pokemones" :key="pokemon.name">
          <div class="card">
            <poke-card :pokemon="pokemon" @guess="checkGuess" />
            <p v-if="pokemon.guessed">{{ pokemon.name }}</p>
          </div>

        </div>

      </div>
    </div>


  </div>
</template>

<script>
import axios from "axios";
import PokeCard from "./components/PokeCard.vue";

export default {
  name: "App",
  components: {
    PokeCard,
  },
  data() {
    return {
      pokemones: [],
      guessInput: "",
    };
  },
  async created() {
    const pokemones = await this.getPokemons();
    this.pokemones = pokemones;
  },
  computed: {
    countGuessed() {
      return this.pokemones.filter((pokemon) => pokemon.guessed).length;
    },
  },
  methods: {
    async getPokemons() {
      const randomOffset = Math.floor(Math.random() * 151);
      const URL_BASE = `https://pokeapi.co/api/v2/pokemon/?limit=60&offset=${randomOffset}`;

      try {
        const responseApi = await axios.get(URL_BASE);
        const pokemonList = responseApi.data.results;

        const shuffledPokemons = pokemonList.sort(() => 0.5 - Math.random());
        const selectedPokemons = shuffledPokemons.slice(0, 20);

        const pokemonResponse = await Promise.all(
          selectedPokemons.map(async (pokemon) => {
            const { data } = await axios.get(pokemon.url);
            return {
              name: data.name,
              image: data.sprites.other.dream_world.front_default,
              guessed: false,
            };
          })
        );

        return pokemonResponse;
      } catch (error) {
        console.error("Error fetching Pokémon data: ", error);
        return [];
      }
    },

    checkGuess(pokemon, guess) {
      if (guess.toLowerCase() === pokemon.name.toLowerCase()) {
        pokemon.guessed = true;
        this.guessInput = "";
      } else {
        alert("Nombre incorrecto, inténtalo de nuevo.");
      }
    },
  },
};
</script>

<style>
img {

  width: clamp(300px, 30vw, 600px);
}

body {
  text-align: center;
  font-family: "Press Start 2P", system-ui;
  background-color: rgb(26, 25, 25);
  color: white;
}

.amarillo {
  color: rgb(198, 191, 86);
}

.card {
  height: 18rem;
  margin: 0.5rem;
  margin-top: 60px;
  text-align: center;
  border-color: red;
  border-width: medium;
}

.poke-bola {
  width: 30px;
}
</style>