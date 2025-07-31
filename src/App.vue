<template>
  <div id="app">
    <!-- Título principal de la Pokédex -->
    <h1>Pokédex con Vue CLI</h1>

    <!-- Campo de búsqueda para filtrar Pokémon por nombre -->
    <input v-model="search" placeholder="Buscar Pokémon" />

    <!-- Contenedor que organiza las tarjetas de Pokémon en una cuadrícula -->
    <div class="grid">
      <!-- Recorremos la lista filtrada de Pokémon y creamos una tarjeta para cada uno -->
      <div
        v-for="pokemon in filteredPokemons"
        :key="pokemon.name"
        class="card"
      >
        <!-- Nombre del Pokémon -->
        <h3>{{ pokemon.name }}</h3>
        <!-- Imagen oficial del Pokémon (frontal) -->
        <img :src="pokemon.image" width="100" />
        <!-- Estadísticas básicas: Ataque y Defensa -->
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
      // Array que almacenará los datos de los Pokémon cargados desde la API
      pokemons: [],
      // Texto de búsqueda que se enlaza al input para filtrar Pokémon en tiempo real
      search: ''
    }
  },
  computed: {
    // Computed property que filtra la lista de Pokémon según el texto introducido en 'search'
    filteredPokemons() {
      return this.pokemons.filter(p =>
        p.name.toLowerCase().includes(this.search.toLowerCase())
      )
    }
  },
  mounted() {
    // al montar el componente, cargamos los Pokémon automáticamente
    this.getPokemons()
  },
  methods: {
    // Método asíncrono que obtiene 10 Pokémon aleatorios desde la API oficial de Pokémon
    async getPokemons() {
      // Creamos un array con 10 IDs aleatorios entre 1 y 150 (Pokémon iniciales)
      const ids = Array.from({ length: 10 }, () =>
        Math.floor(Math.random() * 150 + 1)
      )

      // hacemos fetch a la API para cada ID y esperamos a que todas las promesas terminen
      const results = await Promise.all(
        ids.map(id =>
          fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(res =>
            res.json()
          )
        )
      )

      // Mapeamos la respuesta para quedarnos solo con la información relevante
      this.pokemons = results.map(p => ({
        name: p.name, // Nombre del Pokémon
        image: p.sprites.front_default, // Imagen frontal oficial
        attack: p.stats.find(s => s.stat.name === 'attack').base_stat, // Valor de ataque base
        defense: p.stats.find(s => s.stat.name === 'defense').base_stat // Valor de defensa base
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
  gap: 1rem; /* Espacio entre tarjetas */
}

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

/* Efecto visual verde al pasar el ratón sobre la tarjeta */
.card:hover {
  box-shadow: 0 8px 15px 3px #39ff14;
}

input {
  margin-bottom: 1rem;
  padding: 0.5rem;
  width: 200px;
}
</style>
