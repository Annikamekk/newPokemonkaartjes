<script setup>
import { ref, onMounted, computed } from 'vue'  

const pokemon = ref(null)
const selectedType = ref('grass')

onMounted(async () => {
  const res = await fetch('https://pokeapi.co/api/v2/pokemon/10')
  pokemon.value = await res.json()
})

const formatId = (id) => `#${id.toString().padStart(3, '0')}`
const capitalize = (str) => str.charAt(0).toUpperCase() + str.slice(1)

function getStatValue(statName) {
  if (!pokemon.value) return '-'
  const stat = pokemon.value.stats.find(s => s.stat.name === statName)
  return stat ? stat.base_stat : '-'
}

const typeClass = computed(() => {
  if (!pokemon.value) return 'pokemon-default'
  return `pokemon-${pokemon.value.types[0].type.name}`
})


const typeIcons = {
  grass: '🌿',
  fire: '🔥',
  bug: '🐛',
  poison: '☠️',
  water: '💧',
  electric: '⚡',
  flying: '🕊️',
  fairy: '✨',
  rock: '🪨',
  ground: '🌍',
  ice: '❄️',
  fighting: '🥊',
  psychic: '🔮',
  ghost: '👻',
  dragon: '🐉',
  dark: '🌑',
  steel: '⚙️',
  normal: '⬜',
}

</script>

<template>
<div>
  <section v-if="pokemon" :class="['pokemon', typeClass]">
    <article>
      <h1> {{ capitalize(pokemon.name) }} {{ formatId(pokemon.id) }}</h1>
      <p>HP <span> {{ getStatValue('hp') }}</span></p>
    </article>
  <div>
    <img
      :src="pokemon.sprites.other['official-artwork'].front_default"
      :alt="capitalize(pokemon.name)"
    />
    </div>
  
   <ul>
  <li v-for="typeInfo in pokemon.types" :key="typeInfo.slot">
    <span >
      {{ typeIcons[typeInfo.type.name] || '❓' }}
    </span>

    <span >
      {{ capitalize(typeInfo.type.name) }}
    </span>
  </li>
</ul>

    <aside>
    <p> Attack: {{ getStatValue('attack') }}</p>
    <p> Defense: {{ getStatValue('defense') }}</p>
    </aside>
  </section>
  </div>
</template>

<style scoped></style>
