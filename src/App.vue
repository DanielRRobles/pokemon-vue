<template>
  <div id="app">
    <h1>Pokédex con Vue CLI</h1>
    <input v-model="search" placeholder="Buscar Pokémon" />
    <div class="grid">
      <div
        v-for="pokemon in filteredPokemons"
        :key="pokemon.name"
        class="card"
      >
        <h3>{{ pokemon.name }}</h3>
        <img :src="pokemon.image" width="100" />
        <p>Ataque: {{ pokemon.attack }}</p>
        <p>Defensa: {{ pokemon.defense }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemons: [],
      search: ''
    }
  },
  computed: {
    filteredPokemons() {
      return this.pokemons.filter(p =>
        p.name.toLowerCase().includes(this.search.toLowerCase())
      )
    }
  },
  mounted() {
    this.getPokemons()
  },
  methods: {
    async getPokemons() {
      const ids = Array.from({ length: 10 }, () =>
        Math.floor(Math.random() * 150 + 1)
      )
      const results = await Promise.all(
        ids.map(id =>
          fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(res =>
            res.json()
          )
        )
      )
      this.pokemons = results.map(p => ({
        name: p.name,
        image: p.sprites.front_default,
        attack: p.stats.find(s => s.stat.name === 'attack').base_stat,
        defense: p.stats.find(s => s.stat.name === 'defense').base_stat
      }))
    }
  }
}
</script>

<style>
body {
  font-family: sans-serif;
  background: #f2f2f2;
  padding: 2rem;
}
.grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
.card {
  background: white;
  border-radius: 8px;
  padding: 1rem;
  width: 180px;
  text-align: center;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.1);
}
input {
  margin-bottom: 1rem;
  padding: 0.5rem;
  width: 200px;
}
</style>

