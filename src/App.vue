<template>
  <pokemon-cards v-if="pokemons && pokemons.length" :pokemonsdata="pokemons" @chosen="fetchEvolutions"
    :selectedId="selectedId" />
  <pokemon-cards :pokemonsdata="evolution" />
</template>

<script>
import { ref, onMounted } from 'vue';
import Card from './components/Card.vue';
import PokemonCards from './components/PokemonCards.vue';
const api = 'https://pokeapi.co/api/v2/pokemon'

const IDS = [1, 4, 7]
export default {
  components: {
    PokemonCards,
    Card
  },
  setup() {

    const pokemons = ref(null)
    const evolution = ref(null)

    const selectedId = ref(0)
    const fetchEvolutions = async (pokemon) => {

      evolution.value = await fetchData(
        [pokemon.id + 1, pokemon.id + 2])
      selectedId.value = pokemon.id

    }
    const fetchData = async (IDS) => {
      console.log(IDS);
      const responses = await Promise.all(
        IDS.map(id => window.fetch(`${api}/${id}`))
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
    onMounted(async () => {
      pokemons.value = await fetchData(IDS)
      console.log(pokemons.value);
    })
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
