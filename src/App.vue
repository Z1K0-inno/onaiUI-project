<template>
  <div class="app">
    <header class="app-header">
      <div class="container">
        <div class="header-content">
          <div class="logo">
            <i class="pi pi-home"></i>
            <h1>onaiÜI</h1>
          </div>
          <p class="header-subtitle">Поиск квартир в Астане</p>
        </div>
      </div>
    </header>

    <main class="app-main">
      <div class="container">
        <PropertyFilters
            @filter-change="updateFilters"
            :filtered-count="filteredProperties.length"
        />

        <div v-if="loading" class="loading">
          <i class="pi pi-spin pi-spinner"></i>
          <p>Загрузка объектов...</p>
        </div>

        <div v-else-if="filteredProperties.length === 0" class="no-results">
          <i class="pi pi-search"></i>
          <h3>Квартиры не найдены</h3>
          <p>Попробуйте изменить параметры фильтрации</p>
          <button class="btn-primary" @click="resetAllFilters">
            Сбросить фильтры
          </button>
        </div>

        <div v-else class="properties-grid">
          <PropertyCard
              v-for="property in filteredProperties"
              :key="property.id"
              :property="property"
              @view-details="showPropertyDetails"
          />
        </div>
      </div>
    </main>

    <footer class="app-footer">
      <div class="container">
        <p>© 2026 onaiÜI. Тестовое задание "Фильтры"</p>
      </div>
    </footer>

    <div v-if="selectedProperty" class="modal-overlay" @click="selectedProperty = null">
      <div class="modal-content" @click.stop>
        <button class="modal-close" @click="selectedProperty = null">
          <i class="pi pi-times"></i>
        </button>
        <div class="modal-body">
          <h2>{{ selectedProperty.address }}</h2>
          <img :src="selectedProperty.image" :alt="selectedProperty.address" />
          <div class="modal-details">
            <div class="detail">
              <i class="pi pi-ruler"></i>
              <span>Площадь: <strong>{{ selectedProperty.area }} м²</strong></span>
            </div>
            <div class="detail">
              <i class="pi pi-home"></i>
              <span>Комнат: <strong>{{ selectedProperty.rooms }}</strong></span>
            </div>
            <div class="detail">
              <i class="pi pi-tag"></i>
              <span>Цена: <strong>{{ formatPrice(selectedProperty.price) }}</strong></span>
            </div>
          </div>
          <p class="modal-description">{{ selectedProperty.description }}</p>
          <button class="btn-contact">
            <i class="pi pi-phone"></i> Связаться с продавцом
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import PropertyFilters from './components/PropertyFilters.vue'
import PropertyCard from './components/PropertyCard.vue'
import { debounce } from 'lodash'

const properties = ref([])
const loading = ref(true)
const selectedProperty = ref(null)
const activeFilters = ref({
  areaMin: null,
  areaMax: null,
  roomsMin: null,
  roomsMax: null,
  addressQuery: ''
})

onMounted(async () => {
  try {
    const response = await fetch('/db.json')
    const data = await response.json()
    properties.value = data.properties
  } catch (error) {
    console.error('Ошибка загрузки данных:', error)
  } finally {
    loading.value = false
  }
})

const updateFilters = debounce((filters) => {
  activeFilters.value = filters
}, 300)

const filteredProperties = computed(() => {
  if (!properties.value.length) return []

  return properties.value.filter(property => {
    if (activeFilters.value.areaMin !== null && property.area < activeFilters.value.areaMin) {
      return false
    }
    if (activeFilters.value.areaMax !== null && property.area > activeFilters.value.areaMax) {
      return false
    }

    if (activeFilters.value.roomsMin !== null && property.rooms < activeFilters.value.roomsMin) {
      return false
    }
    if (activeFilters.value.roomsMax !== null && property.rooms > activeFilters.value.roomsMax) {
      return false
    }

    if (activeFilters.value.addressQuery &&
        !property.address.toLowerCase().includes(activeFilters.value.addressQuery)) {
      return false
    }

    return true
  })
})

const formatPrice = (price) => {
  return new Intl.NumberFormat('ru-RU').format(price) + ' ₸'
}

const showPropertyDetails = (property) => {
  selectedProperty.value = property
}

const resetAllFilters = () => {
  activeFilters.value = {
    areaMin: null,
    areaMax: null,
    roomsMin: null,
    roomsMax: null,
    addressQuery: ''
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  min-height: 100vh;
  color: #333;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-header {
  background: linear-gradient(135deg, #2c3e50, #34495e);
  color: white;
  padding: 24px 0;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.header-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 16px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo i {
  font-size: 2.5rem;
  color: #3498db;
}

.logo h1 {
  font-size: 1.8rem;
  font-weight: 700;
  background: linear-gradient(135deg, #3498db, #2ecc71);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.header-subtitle {
  color: #bdc3c7;
  font-size: 1rem;
}

.app-main {
  flex: 1;
  padding: 32px 0;
}

.properties-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 30px;
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 60px 0;
  color: #7f8c8d;
}

.loading i {
  font-size: 3rem;
  color: #3498db;
  margin-bottom: 20px;
}

.loading p {
  font-size: 1.2rem;
}

.no-results {
  text-align: center;
  padding: 60px 20px;
  background: white;
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.no-results i {
  font-size: 4rem;
  color: #bdc3c7;
  margin-bottom: 24px;
}

.no-results h3 {
  color: #2c3e50;
  margin-bottom: 12px;
}

.no-results p {
  color: #7f8c8d;
  margin-bottom: 32px;
}

.no-results .btn-primary {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 14px 28px;
  background: linear-gradient(135deg, #3498db, #2980b9);
  color: white;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.no-results .btn-primary:hover {
  background: linear-gradient(135deg, #2980b9, #1c5d87);
  transform: translateY(-2px);
}

.app-footer {
  background: #2c3e50;
  color: #ecf0f1;
  padding: 24px 0;
  text-align: center;
  margin-top: auto;
}

/* Модальное окно */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(4px);
  animation: fadeIn 0.3s ease;
}

.modal-content {
  background: white;
  border-radius: 20px;
  padding: 32px;
  max-width: 500px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  animation: slideIn 0.3s ease;
}

@keyframes slideIn {
  from {
    transform: translateY(-30px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

.modal-close {
  position: absolute;
  top: 16px;
  right: 16px;
  background: #f8f9fa;
  border: none;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #7f8c8d;
  transition: all 0.3s ease;
}

.modal-close:hover {
  background: #e74c3c;
  color: white;
}

.modal-body h2 {
  color: #2c3e50;
  margin-bottom: 24px;
  font-size: 1.5rem;
}

.modal-body img {
  width: 100%;
  height: 250px;
  object-fit: cover;
  border-radius: 12px;
  margin-bottom: 24px;
}

.modal-details {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  margin-bottom: 24px;
  background: #f8f9fa;
  padding: 20px;
  border-radius: 12px;
}

.detail {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.detail i {
  font-size: 1.5rem;
  color: #3498db;
  margin-bottom: 8px;
}

.detail span {
  color: #5d6d7e;
}

.detail strong {
  color: #2c3e50;
  font-size: 1.1rem;
}

.modal-description {
  color: #5d6d7e;
  line-height: 1.6;
  margin-bottom: 32px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 12px;
}

.modal-body .btn-contact {
  width: 100%;
  padding: 16px;
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  font-size: 1.1rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  transition: all 0.3s ease;
}

.modal-body .btn-contact:hover {
  background: linear-gradient(135deg, #27ae60, #219653);
  transform: translateY(-2px);
}

/* Адаптивность */
@media (max-width: 1200px) {
  .properties-grid {
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 24px;
  }
}

@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    text-align: center;
  }

  .properties-grid {
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 20px;
  }

  .modal-content {
    padding: 24px;
    width: 95%;
  }

  .modal-details {
    grid-template-columns: 1fr;
    gap: 12px;
  }
}

@media (max-width: 480px) {
  .container {
    padding: 0 16px;
  }

  .properties-grid {
    grid-template-columns: 1fr;
  }

  .app-header {
    padding: 20px 0;
  }

  .logo h1 {
    font-size: 1.5rem;
  }

  .modal-content {
    padding: 20px;
  }
}
</style>