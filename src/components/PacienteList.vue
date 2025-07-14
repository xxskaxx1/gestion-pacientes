<template>
  <div class="paciente-list">
    <div v-if="pacientes.length === 0" class="empty-message">
      No hay pacientes registrados
    </div>
    
    <table v-else class="pacientes-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Edad</th>
          <th>Tipo Sangre</th>
          <th>Estado</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <paciente-item 
          v-for="paciente in pacientes" 
          :key="paciente.id" 
          :paciente="paciente"
          @edit="$emit('edit', paciente)"
          @delete="$emit('delete', paciente)"
        />
      </tbody>
    </table>
  </div>
</template>

<script>
import PacienteItem from './PacienteItem.vue'

export default {
  components: {
    PacienteItem
  },
  props: {
    pacientes: {
      type: Array,
      required: true
    }
  }
}
</script>

<style scoped>
.paciente-list {
  margin-top: 20px;
}

.empty-message {
  text-align: center;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 4px;
  color: #7f8c8d;
}

.pacientes-table {
  width: 100%;
  border-collapse: collapse;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  overflow-x: auto;
}

.pacientes-table th {
  background-color: #3498db;
  color: white;
  padding: 12px;
  text-align: left;
}

.pacientes-table td {
  padding: 12px;
  border-bottom: 1px solid #ddd;
}

.pacientes-table tr:nth-child(even) {
  background-color: #f8f9fa;
}

.pacientes-table tr:hover {
  background-color: #e8f4fc;
}

@media (max-width: 768px) {
  .pacientes-table {
    display: block;
    overflow-x: auto;
  }
}
</style>