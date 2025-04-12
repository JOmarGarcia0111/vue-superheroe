<script setup>
import { ref } from 'vue'
import axios from 'axios'

// Variables reactivas
const selectedTable = ref('superheroes')
const allRecords = ref([])
const searchId = ref('')
const recordById = ref(null)

// Función para consultar todos los registros de la tabla seleccionada
async function fetchAll() {
  try {
    const response = await axios.get(`http://localhost:8000/api/${selectedTable.value}`)
    allRecords.value = response.data
  } catch (error) {
    allRecords.value = []
    console.error('Error al obtener todos los registros:', error)
  }
}

// Función para buscar un registro por ID
async function fetchById() {
  if (!searchId.value) {
    recordById.value = { error: 'Por favor ingresa un ID válido' }
    return
  }

  try {
    const response = await axios.get(`http://localhost:8000/api/${selectedTable.value}/${searchId.value}`)
    recordById.value = response.data
  } catch (error) {
    recordById.value = { error: 'No se encontró el registro' }
    console.error('Error al buscar por ID:', error)
  }
}
</script>

<template>
  <h1>Superheroes API</h1>

  <!-- Sección para consultar todos los registros -->
  <section>
    <h2>Consulta todos los registros</h2>

    <!-- Selector de tabla -->
   <select v-model="selectedTable">
  <option value="superheroes">Superheroes</option>
  <option value="gender">Genders</option>
  <option value="universe">Universes</option>
</select>

    <!-- Botón para consultar -->
    <button @click="fetchAll">Consultar</button>

    <!-- Lista de resultados -->
    <ul v-if="allRecords.length > 0">
      <li v-for="item in allRecords" :key="item.id">
        {{ item }}
      </li>
    </ul>

    <!-- Mensaje si no hay resultados -->
    <p v-else>Sin resultados.</p>
  </section>

  <!-- Sección para buscar por ID -->
  <section>
    <h2>Buscar por ID</h2>
    <input type="text" v-model="searchId" placeholder="Ingresa ID" />
    <button @click="fetchById">Buscar</button>

    <!-- Mostrar resultado de búsqueda -->
    <pre v-if="recordById">{{ JSON.stringify(recordById, null, 2) }}</pre>
  </section>
</template>
