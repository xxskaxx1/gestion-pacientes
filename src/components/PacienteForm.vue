<template>
  <div>
    <!-- Botón para activar el modal -->
    <button 
      v-if="!showModal" 
      @click="openModal(null)" 
      class="btn-new-patient"
    >
      ＋ Nuevo Paciente
    </button>

    <!-- Modal -->
    <transition name="modal">
      <div v-if="showModal" class="modal-backdrop" @click.self="closeModal">
        <div class="modal-container">
          <div class="modal-header">
            <h3>{{ isEditing ? 'Editar Paciente' : 'Nuevo Paciente' }}</h3>
            <button class="modal-close" @click="closeModal">&times;</button>
          </div>

          <div class="modal-content">
            <form @submit.prevent="handleSubmit">
              <!-- Campo Nombre -->
              <div class="form-group">
                <label for="nombre">Nombre*</label>
                <input 
                  type="text" 
                  id="nombre" 
                  v-model="localPaciente.nombre" 
                  @blur="validateField('nombre')"
                  :class="{ 'invalid': errors.nombre }"
                >
                <span class="error" v-if="errors.nombre">{{ errors.nombre }}</span>
              </div>
              
              <!-- Campo Apellido -->
              <div class="form-group">
                <label for="apellido">Apellido*</label>
                <input 
                  type="text" 
                  id="apellido" 
                  v-model="localPaciente.apellido" 
                  @blur="validateField('apellido')"
                  :class="{ 'invalid': errors.apellido }"
                >
                <span class="error" v-if="errors.apellido">{{ errors.apellido }}</span>
              </div>
              
              <!-- Campo Edad -->
              <div class="form-group">
                <label for="edad">Edad*</label>
                <input 
                  type="number" 
                  id="edad" 
                  v-model.number="localPaciente.edad" 
                  @blur="validateField('edad')"
                  :class="{ 'invalid': errors.edad }"
                >
                <span class="error" v-if="errors.edad">{{ errors.edad }}</span>
              </div>
              
              <!-- Campo Tipo de Sangre -->
              <div class="form-group">
                <label for="tipoSangre">Tipo de Sangre*</label>
                <select 
                  id="tipoSangre" 
                  v-model="localPaciente.tipoSangre"
                  @blur="validateField('tipoSangre')"
                  :class="{ 'invalid': errors.tipoSangre }"
                >
                  <option value="">Seleccione...</option>
                  <option v-for="type in bloodTypes" :key="type" :value="type">
                    {{ type }}
                  </option>
                </select>
                <span class="error" v-if="errors.tipoSangre">{{ errors.tipoSangre }}</span>
              </div>
              
              <!-- Campo Estado (Activo/Inactivo) -->
              <div class="form-group checkbox-group">
                <input 
                  type="checkbox" 
                  id="activo" 
                  v-model="localPaciente.activo"
                >
                <label for="activo">Paciente Activo</label>
              </div>
              
              <!-- Botones de acción -->
              <div class="form-actions">
                <button type="submit" class="btn-save">
                  {{ isEditing ? 'Actualizar' : 'Guardar' }}
                </button>
                <button type="button" class="btn-cancel" @click="closeModal">
                  Cancelar
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </transition>

    <!-- Notificación de éxito -->
    <transition name="notification">
      <div v-if="showSuccessNotification" class="notification success">
        {{ successMessage }}
        <button @click="showSuccessNotification = false" class="close-btn">&times;</button>
      </div>
    </transition>

    <!-- Notificación de error -->
    <transition name="notification">
      <div v-if="showErrorNotification" class="error-notification">
        <span class="notification-icon">⚠️</span>
        <span class="notification-message">{{ errorMessage }}</span>
        <button @click="showErrorNotification = false" class="close-btn">
          &times;
        </button>
      </div>
    </transition>

  </div>
</template>

<script>
export default {
  props: {
    paciente: {
      type: Object,
      default: () => null
    },
    bloodTypes: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      showModal: false,
      showSuccessNotification: false,
      showErrorNotification: false,
      successMessage: '',
      localPaciente: {
        id: null,
        nombre: '',
        apellido: '',
        edad: null,
        tipoSangre: '',
        activo: true
      },
      errors: {
        nombre: '',
        apellido: '',
        edad: '',
        tipoSangre: ''
      }
    }
  },
  computed: {
    isEditing() {
      return this.localPaciente.id !== null
    }
  },
  methods: {
    openModal(paciente) {
      if (paciente) {
        this.localPaciente = { ...paciente }
      } else {
        this.resetForm()
      }
      this.showModal = true
    },
    closeModal() {
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.localPaciente = {
        id: null,
        nombre: '',
        apellido: '',
        edad: null,
        tipoSangre: '',
        activo: true
      }
      this.clearErrors()
    },
    clearErrors() {
      this.errors = {
        nombre: '',
        apellido: '',
        edad: '',
        tipoSangre: ''
      }
    },
    validateField(field) {
      switch(field) {
        case 'nombre':
          this.errors.nombre = this.localPaciente.nombre.length >= 2 ? '' : 'Mínimo 2 caracteres'
          break
        case 'apellido':
          this.errors.apellido = this.localPaciente.apellido.length >= 2 ? '' : 'Mínimo 2 caracteres'
          break
        case 'edad':
          this.errors.edad = this.localPaciente.edad >= 18 && this.localPaciente.edad <= 120 ? '' : 'Edad debe ser entre 18 y 120'
          break
        case 'tipoSangre':
          this.errors.tipoSangre = this.localPaciente.tipoSangre ? '' : 'Seleccione un tipo de sangre'
          break
      }
    },
    validateForm() {
      this.validateField('nombre')
      this.validateField('apellido')
      this.validateField('edad')
      this.validateField('tipoSangre')
      
      return !Object.values(this.errors).some(error => error)
    },
    handleSubmit() {
      if (this.validateForm()) {
        this.$emit('save', { ...this.localPaciente })
        this.showNotification(
          this.isEditing 
            ? 'Paciente actualizado correctamente' 
            : 'Paciente creado correctamente'
        )
        this.closeModal()
      } else {
        this.showFormError('Por favor complete todos los campos correctamente')
      }
    },

    showNotification(message) {
      this.successMessage = message
      this.showSuccessNotification = true
      setTimeout(() => {
        this.showSuccessNotification = false
      }, 3000)
    },

    showFormError(message) {
      this.errorMessage = message
      this.showErrorNotification = true
      setTimeout(() => {
        this.showErrorNotification = false
      }, 3000)
    },
    
  }
}
</script>

<style scoped>
/* Estilos del modal */
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  overflow: hidden;
}

.modal-container {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 95%;
  max-width: 600px;
  max-height: 95vh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.modal-header {
  padding: 15px 20px;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-shrink: 0;
}

.modal-content {
  padding: 20px;
  overflow-y: auto;
  flex-grow: 1;
}

.modal-close {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #7f8c8d;
}

/* Estilos del formulario */
.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: 500;
  color: #34495e;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  box-sizing: border-box;
}

.form-group input.invalid,
.form-group select.invalid {
  border-color: #e74c3c;
}

.error {
  color: #e74c3c;
  font-size: 13px;
  margin-top: 5px;
  display: block;
}

.checkbox-group {
  display: flex;
  align-items: center;
}

.checkbox-group input {
  width: auto;
  margin-right: 10px;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 20px;
}

.btn-save {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-save:hover {
  background-color: #2980b9;
}

.btn-cancel {
  background-color: #95a5a6;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-cancel:hover {
  background-color: #7f8c8d;
}

/* Botón nuevo paciente */
.btn-new-patient {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
  transition: background-color 0.3s;
}

.btn-new-patient:hover {
  background-color: #3e8e41;
}

/* Notificación */
.notification {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 15px 20px;
  border-radius: 4px;
  color: white;
  display: flex;
  align-items: center;
  justify-content: space-between;
  min-width: 250px;
  box-shadow: 0 3px 10px rgba(0,0,0,0.2);
  z-index: 1001;
}

.notification.success {
  background-color: #4CAF50;
}

.error-notification {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 15px 20px;
  background-color: #f44336;
  color: white;
  border-radius: 4px;
  display: flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.2);
  z-index: 1001;
  animation: slideIn 0.3s ease-out;
}

.close-btn {
  background: none;
  border: none;
  color: white;
  font-size: 20px;
  cursor: pointer;
  margin-left: 10px;
}

/* Transiciones */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .modal-container,
.modal-leave-active .modal-container {
  transition: transform 0.3s ease;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  transform: scale(0.95);
}

.notification-enter-active,
.notification-leave-active {
  transition: all 0.3s ease;
}

.notification-enter-from,
.notification-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
</style>