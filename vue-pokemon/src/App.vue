<script setup>
import { ref, onMounted, watch, computed } from 'vue'  

const pokemon = ref(null)
const currentPokemonId = ref(1)

async function fetchPokemon() {
  const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${currentPokemonId.value}`)
  pokemon.value = await res.json()
}

onMounted(fetchPokemon)

watch(currentPokemonId, fetchPokemon)


function prevPokemon() {
  if (currentPokemonId.value > 1) currentPokemonId.value--
}

function nextPokemon() {
  if (currentPokemonId.value < 1010) currentPokemonId.value++ // max Pokémon ID (afhankelijk van PokeAPI)
}


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

function handleMouseMove(e) {
  const card = cardRef.value
  if (!card) return

  const rect = card.getBoundingClientRect()
  const x = (e.clientX - rect.left) / rect.width
  const y = (e.clientY - rect.top) / rect.height

  // max 15 graden rotatie
  const rotateX = (y - 0.5) * 30
  const rotateY = (x - 0.5) * -30

  card.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg)`
}

function handleMouseLeave() {
  const card = cardRef.value
  if (!card) return
  card.style.transform = 'rotateX(0deg) rotateY(0deg)'
}
</script>

<template >
  <div class='parent'>
    <section v-if="pokemon" :class="['pokemon', typeClass]">
      <article>
        <h1> {{ capitalize(pokemon.name) }} {{ formatId(pokemon.id) }}</h1>
        <p>HP 
          <span> {{ getStatValue('hp') }}</span>
        </p>
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
          <p>
            {{ capitalize(typeInfo.type.name) }}
          </p>
        </li>
      </ul>
      <aside>
        <p> Attack: {{ getStatValue('attack') }}</p>
        <p> Defense: {{ getStatValue('defense') }}</p>
      </aside>
    </section>
  </div>
  <section>
  <button @click="prevPokemon">Vorige</button>
  <button @click="nextPokemon">Volgende</button>
</section>

</template>

<style scoped></style>
