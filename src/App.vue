
<template>
  <!-- <suspense> -->
  Hlo
  {{ evolution }}{{ pokemons }}
  <pokemon-cards :pokemons="pokemons" @chosen="fetchEvolutions" :selectedId="selectedId" />

  <pokemon-cards :pokemons="evolution" />

  <div class="cards">
    <div v-for="pokemon in evolution" :key="pokemon.id" @click="fetchEvolutions(pokemon)">
      <!-- <template v-slot:title> -->
        {{ pokemon.name }} #{{ pokemon.id }}
      <!-- </template> -->


      <!-- <template v-slot:content> -->

        <img :src="pokemon.sprites">
      <!-- </template> -->

      <!-- <template v-slot:description> -->
        <div v-for="type in pokemon.types" :key="type">
          {{ type }}
        </div>
    
</div>
    
  </div>


</template>


<script>
import { ref, onMounted } from 'vue';
// import Card from './components/Card.vue';
import PokemonCards from './components/PokemonCards.vue';
const api = 'https://pokeapi.co/api/v2/pokemon'

const IDS = [1, 4, 7]
export default {
  components: {
    PokemonCards,
    // Card
  },
  setup() {

    const pokemons = ref(null)
    const evolution = ref(null)

    const selectedId = ref(0)
     fetchEvolutions = async (pokemon) => {
      evolution.value = await fetchData(
        [pokemon.id + 1, pokemon.id + 2])
      selectedId.value = pokemon.id
      console.log(selectedId.value);
    }

    onMounted(async () => {
      fetchData(IDS)
      pokemons.value = await fetchData(IDS)

    })
    const fetchData = async (ids) => {
      const responses = await Promise.all(
        ids.map(id => window.fetch(`${api}/${id}`))
      )
      const json = await Promise.all(
        responses.map(data => data.json())
      )
      return json.map(dataum => ({
        id: dataum.id,
        name: dataum.name,
        sprites: dataum.sprites.other['official-artwork'].front_default,
        types: dataum.types.map(type => type.type.name)
      }))
    }
    return {
      pokemons,
      evolution,
      selectedId,
      fetchEvolutions,
      fetchData
    }
  }
}
</script>

<style>

</style>
