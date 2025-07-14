<template>
  <div class="search-filter">
    <div class="search-box">
      <input 
        type="text" 
        placeholder="Buscar por nombre..." 
        v-model="searchTerm"
        @input="handleSearch"
      >
      <span class="search-icon">🔍</span>
    </div>
    
    <div class="filters">
      <select v-model="bloodType" @change="handleFilter">
        <option value="">Todos los tipos</option>
        <option v-for="type in bloodTypes" :key="type" :value="type">
          {{ type }}
        </option>
      </select>
      
      <select v-model="status" @change="handleFilter">
        <option value="">Todos los estados</option>
        <option value="true">Activos</option>
        <option value="false">Inactivos</option>
      </select>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    bloodTypes: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      searchTerm: '',
      bloodType: '',
      status: ''
    }
  },
  methods: {
    handleSearch() {
      this.$emit('search', this.searchTerm)
    },
    handleFilter() {
      this.$emit('filter', {
        bloodType: this.bloodType,
        status: this.status
      })
    }
  }
}
</script>

<style scoped>
.search-filter {
  display: flex;
  gap: 55px;
  flex-wrap: wrap;
}

.search-box {
  position: relative;
  flex: 1;
  min-width: 200px;
}

.search-box input {
  width: 100%;
  padding: 10px 10px 10px 35px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.search-icon {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
}

.filters {
  display: flex;
  gap: 10px;
}

.filters select {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: white;
}

@media (max-width: 600px) {
  .search-filter {
    flex-direction: column;
  }
  
  .filters {
    flex-direction: column;
  }
}
</style>