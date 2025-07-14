<template>
  <div id="app">
    <div class="container">
      <h1>🏥 Sistema de Gestión de Pacientes</h1>
      
      <!-- Barra de acciones y filtros -->
      <div class="action-bar">
        <paciente-form
          ref="pacienteForm"
          :blood-types="bloodTypes"
          @save="handleSave"
        />
        <search-filter 
          @search="handleSearch"
          @filter="handleFilter"
          :blood-types="bloodTypes"
        />
      </div>

      <!-- Lista de pacientes -->
      <paciente-list 
        :pacientes="filteredPacientes"
        @edit="handleEdit"
        @delete="handleDelete"
      />

      <!-- Modal de confirmación -->
      <confirm-dialog 
        v-if="showConfirm"
        :message="confirmMessage"
        @confirm="confirmAction"
        @cancel="showConfirm = false"
      />

      <!-- Notificaciones-->
      <app-notification 
        v-if="showNotification"
        :message="notificationMessage"
        :type="notificationType"
        @close="showNotification = false"
      />
    </div>
  </div>
</template>

<script>
import PacienteList from './components/PacienteList.vue'
import PacienteForm from './components/PacienteForm.vue'
import SearchFilter from './components/SearchFilter.vue'
import ConfirmDialog from './components/ConfirmDialog.vue'
import AppNotification from './components/AppNotification.vue'

export default {
  name: 'App',
  components: {
    PacienteList,
    PacienteForm,
    SearchFilter,
    ConfirmDialog,
    AppNotification
  },
  data() {
    return {
      pacientes: [],
      showConfirm: false,
      confirmMessage: '',
      actionToConfirm: null,
      searchTerm: '',
      filterBloodType: '',
      filterStatus: '',
      bloodTypes: ['A+', 'A-', 'B+', 'B-', 'AB+', 'AB-', 'O+', 'O-'],
      //showNotification: false,
      notificationMessage: '',
      notificationType: 'success'
    }
  },
  created() {
    this.loadPacientes()
  },
  computed: {
    filteredPacientes() {
      return this.pacientes.filter(paciente => {
        const matchesSearch = paciente.nombre.toLowerCase().includes(this.searchTerm.toLowerCase()) || 
                            paciente.apellido.toLowerCase().includes(this.searchTerm.toLowerCase())
        const matchesBloodType = this.filterBloodType ? paciente.tipoSangre === this.filterBloodType : true
        const matchesStatus = this.filterStatus !== '' ? paciente.activo === (this.filterStatus === 'true') : true
        
        return matchesSearch && matchesBloodType && matchesStatus
      })
    }
  },
  methods: {
    loadPacientes() {
      const saved = localStorage.getItem('pacientes')
      if (saved) {
        this.pacientes = JSON.parse(saved)
      } else {
        // Datos iniciales
        this.pacientes = [
          {
            id: 1,
            nombre: "Juan",
            apellido: "Pérez",
            edad: 35,
            tipoSangre: "O+",
            activo: true
          },
          {
            id: 2,
            nombre: "María",
            apellido: "García",
            edad: 28,
            tipoSangre: "A-",
            activo: false
          },
          {
            id: 3,
            nombre: "Carlos",
            apellido: "López",
            edad: 42,
            tipoSangre: "AB+",
            activo: true
          }
        ]
        this.savePacientes()
      }
    },
    savePacientes() {
      localStorage.setItem('pacientes', JSON.stringify(this.pacientes))
    },
    handleSave(paciente) {
      if (paciente.id) {
        // Edición
        const index = this.pacientes.findIndex(p => p.id === paciente.id)
        this.pacientes.splice(index, 1, paciente)
        this.showSuccess('Paciente actualizado correctamente')
      } else {
        // Nuevo
        paciente.id = this.pacientes.length > 0 
          ? Math.max(...this.pacientes.map(p => p.id)) + 1 
          : 1
        this.pacientes.push(paciente)
        this.showSuccess('Paciente creado correctamente')
      }
      
      this.savePacientes()
    },
    handleEdit(paciente) {
      this.$refs.pacienteForm.openModal(paciente)
    },
    handleDelete(paciente) {
      this.actionToConfirm = () => {
        try {
          const index = this.pacientes.findIndex(p => p.id === paciente.id)
          this.pacientes.splice(index, 1)
          this.showSuccess('Paciente eliminado correctamente')
          this.savePacientes()
        } catch (error) {
          this.showSuccess('Error al eliminar el paciente')
        }
      }
      this.confirmMessage = `¿Estás seguro de eliminar a ${paciente.nombre} ${paciente.apellido}?`
      this.showConfirm = true
    },
    confirmAction() {
      this.actionToConfirm()
      this.showConfirm = false
      //this.showSuccess('Paciente eliminado correctamente')
    },
    handleSearch(term) {
      this.searchTerm = term
    },
    handleFilter({bloodType, status}) {
      this.filterBloodType = bloodType
      this.filterStatus = status
    },
    showSuccess(message) {
      this.notificationMessage = message
      this.notificationType = 'success'
      this.showNotification = true
    },
    showError(message) {
      this.notificationMessage = message
      this.notificationType = 'error'
      this.showNotification = true
    }
  }
}
</script>

<style>
/* Estilos globales */
#app {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.6;
  color: #333;
  background: #f5f7fa;
  min-height: 100vh;
  padding: 20px;
}

.container {
  max-width: 1000px;
  margin: 0 auto;
  background: white;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  padding: 20px;
}

h1 {
  color: #2c3e50;
  text-align: center;
  margin-bottom: 30px;
  padding-bottom: 15px;
  border-bottom: 2px solid #eee;
}

.action-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
  gap: 15px;
}

@media (max-width: 768px) {
  .action-bar {
    flex-direction: column;
  }
  
  .container {
    padding: 15px;
  }
}
</style>