<template>
  <div id="app">
    <h1>Pokédex con Vue CLI</h1>
    <!-- Campo de búsqueda que actualiza el modelo 'search' en tiempo real -->
    <input v-model="search" placeholder="Buscar Pokémon" />
    
    <!-- Contenedor para mostrar las tarjetas filtradas de Pokémon -->
    <div class="grid">
      <!-- Recorremos la lista filtrada de pokemons para crear una tarjeta por cada uno -->
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
      // Array donde almacenamos los datos de los Pokémon cargados
      pokemons: [],
      // Texto que el usuario escribe para filtrar la lista
      search: ''
    }
  },
  computed: {
    // Computed que devuelve la lista de pokemons filtrada según el texto de búsqueda
    filteredPokemons() {
      return this.pokemons.filter(p =>
        p.name.toLowerCase().includes(this.search.toLowerCase())
      )
    }
  },
  mounted() {
    // Hook que se ejecuta al montar el componente, para cargar los Pokémon
    this.getPokemons()
  },
  methods: {
    // Método asíncrono que obtiene 10 Pokémon aleatorios de la API
    async getPokemons() {
      const ids = Array.from({ length: 10 }, () =>
        Math.floor(Math.random() * 150 + 1) // IDs del 1 al 150
      )
      // Hacemos todas las peticiones y esperamos a que terminen
      const results = await Promise.all(
        ids.map(id =>
          fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(res =>
            res.json()
          )
        )
      )
      // Mapeamos la respuesta para quedarnos solo con los datos que queremos mostrar
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
/* Estilos generales para el body, letra y fondo */
body {
  font-family: sans-serif;
  background: #f2f2f2;
  padding: 2rem;
}
/* Contenedor que organiza las tarjetas en filas flexibles */
.grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
/* Estilo de cada tarjeta individual de Pokémon */
.card {
  background: white;
  border-radius: 8px;
  padding: 1rem;
  width: 180px;
  text-align: center;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.1);
  position: relative;
  transition: box-shadow 0.3s ease;
  cursor: pointer;
}
/* Efecto hover con sombra verde neón */
.card:hover {
  box-shadow: 0 8px 15px 3px #39ff14;
}
/* Estilos para el campo de búsqueda */
input {
  margin-bottom: 1rem;
  padding: 0.5rem;
  width: 200px;
}
</style>
