<template>
  <tr>
    <td>{{ paciente.id }}</td>
    <td>{{ paciente.nombre }} {{ paciente.apellido }}</td>
    <td>{{ paciente.edad }}</td>
    <td>{{ paciente.tipoSangre }}</td>
    <td>
      <span :class="['status-badge', paciente.activo ? 'active' : 'inactive']">
        {{ paciente.activo ? 'Activo' : 'Inactivo' }}
      </span>
    </td>
    <td class="actions">
      <button @click="$emit('edit')" class="btn-edit">✏️ Editar</button>
      <button @click="$emit('delete')" class="btn-delete">🗑️ Eliminar</button>
    </td>
  </tr>
</template>

<script>
export default {
  props: {
    paciente: {
      type: Object,
      required: true
    }
  }
}
</script>

<style scoped>
.status-badge {
  padding: 5px 10px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 500;
}

.status-badge.active {
  background-color: #d4edda;
  color: #155724;
}

.status-badge.inactive {
  background-color: #f8d7da;
  color: #721c24;
}

.actions {
  display: flex;
  gap: 5px;
}

.btn-edit {
  background-color: #ffc107;
  color: #212529;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-edit:hover {
  background-color: #e0a800;
}

.btn-delete {
  background-color: #dc3545;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-delete:hover {
  background-color: #c82333;
}

@media (max-width: 600px) {
  .actions {
    flex-direction: column;
  }
  
  .btn-edit, .btn-delete {
    padding: 3px 6px;
    font-size: 12px;
  }
}
</style>