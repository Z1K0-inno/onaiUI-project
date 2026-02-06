<template>
  <div class="filters-container">
    <div class="filters-header">
      <h2>🔍 Поиск квартир в Астане</h2>
      <p class="subtitle">Найдите идеальную квартиру с помощью фильтров</p>
    </div>

    <div class="filters-grid">
      <!-- Площадь -->
      <div class="filter-group">
        <label for="area-min">Площадь, м²</label>
        <div class="range-inputs">
          <div class="input-with-icon">
            <i class="pi pi-arrow-right"></i>
            <input
                id="area-min"
                v-model="areaMin"
                type="number"
                placeholder="От"
                min="0"
                @input="validateArea"
            />
          </div>
          <div class="input-with-icon">
            <i class="pi pi-arrow-left"></i>
            <input
                v-model="areaMax"
                type="number"
                placeholder="До"
                min="0"
                @input="validateArea"
            />
          </div>
        </div>
        <div class="range-values">
          <span>{{ areaMin || 0 }} - {{ areaMax || '∞' }} м²</span>
        </div>
      </div>

      <!-- Комнаты -->
      <div class="filter-group">
        <label for="rooms-min">Количество комнат</label>
        <div class="range-inputs">
          <div class="input-with-icon">
            <i class="pi pi-home"></i>
            <input
                id="rooms-min"
                v-model="roomsMin"
                type="number"
                placeholder="От"
                min="1"
                max="10"
                @input="validateRooms"
            />
          </div>
          <div class="input-with-icon">
            <i class="pi pi-home"></i>
            <input
                v-model="roomsMax"
                type="number"
                placeholder="До"
                min="1"
                max="10"
                @input="validateRooms"
            />
          </div>
        </div>
        <div class="range-values">
          <span>{{ roomsMin || 1 }} - {{ roomsMax || '∞' }} комнат</span>
        </div>
      </div>

      <!-- Адрес -->
      <div class="filter-group full-width">
        <label for="address-search">Адрес</label>
        <div class="input-with-icon search-input">
          <i class="pi pi-search"></i>
          <input
              id="address-search"
              v-model="addressQuery"
              type="text"
              placeholder="Введите улицу или район..."
              @input="sanitizeAddress"
          />
          <button v-if="addressQuery" class="clear-btn" @click="addressQuery = ''">
            <i class="pi pi-times"></i>
          </button>
        </div>
      </div>
    </div>

    <!-- Кнопки действий -->
    <div class="actions">
      <button class="btn-primary" @click="applyFilters">
        <i class="pi pi-search"></i> Найти квартиры
      </button>
      <button class="btn-secondary" @click="resetFilters">
        <i class="pi pi-refresh"></i> Сбросить фильтры
      </button>
    </div>

    <!-- Статистика -->
    <div class="stats" v-if="filteredCount > 0">
      <p>Найдено квартир: <strong>{{ filteredCount }}</strong></p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const emit = defineEmits(['filter-change'])

// Реактивные данные фильтров
const areaMin = ref('')
const areaMax = ref('')
const roomsMin = ref('')
const roomsMax = ref('')
const addressQuery = ref('')

// Валидация площади
const validateArea = () => {
  if (areaMin.value && areaMax.value && parseInt(areaMin.value) > parseInt(areaMax.value)) {
    const temp = areaMin.value
    areaMin.value = areaMax.value
    areaMax.value = temp
  }
}

// Валидация комнат
const validateRooms = () => {
  if (roomsMin.value && roomsMax.value && parseInt(roomsMin.value) > parseInt(roomsMax.value)) {
    const temp = roomsMin.value
    roomsMin.value = roomsMax.value
    roomsMax.value = temp
  }
}

// Санитизация адреса (защита от XSS)
const sanitizeAddress = () => {
  addressQuery.value = addressQuery.value.replace(/[<>]/g, '')
}

// Применить фильтры
const applyFilters = () => {
  const filters = {
    areaMin: areaMin.value ? parseInt(areaMin.value) : null,
    areaMax: areaMax.value ? parseInt(areaMax.value) : null,
    roomsMin: roomsMin.value ? parseInt(roomsMin.value) : null,
    roomsMax: roomsMax.value ? parseInt(roomsMax.value) : null,
    addressQuery: addressQuery.value.toLowerCase().trim()
  }
  emit('filter-change', filters)
}

// Сбросить фильтры
const resetFilters = () => {
  areaMin.value = ''
  areaMax.value = ''
  roomsMin.value = ''
  roomsMax.value = ''
  addressQuery.value = ''
  emit('filter-change', {
    areaMin: null,
    areaMax: null,
    roomsMin: null,
    roomsMax: null,
    addressQuery: ''
  })
}

// Отслеживание изменений в реальном времени
watch([areaMin, areaMax, roomsMin, roomsMax, addressQuery], () => {
  applyFilters()
})

defineProps({
  filteredCount: {
    type: Number,
    default: 0
  }
})
</script>

<style scoped>
.filters-container {
  background: white;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  margin-bottom: 32px;
}

.filters-header {
  margin-bottom: 28px;
}

.filters-header h2 {
  color: #2c3e50;
  margin: 0 0 8px 0;
  font-size: 1.5rem;
}

.subtitle {
  color: #7f8c8d;
  margin: 0;
  font-size: 0.95rem;
}

.filters-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 24px;
  margin-bottom: 28px;
}

.filter-group {
  display: flex;
  flex-direction: column;
}

.filter-group.full-width {
  grid-column: 1 / -1;
}

.filter-group label {
  font-weight: 600;
  margin-bottom: 10px;
  color: #34495e;
  font-size: 0.95rem;
}

.range-inputs {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.input-with-icon {
  position: relative;
  display: flex;
  align-items: center;
}

.input-with-icon i {
  position: absolute;
  left: 12px;
  color: #95a5a6;
  z-index: 1;
}

.input-with-icon input {
  width: 100%;
  padding: 12px 12px 12px 36px;
  border: 2px solid #e0e6ed;
  border-radius: 10px;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  background: white;
}

.input-with-icon input:focus {
  outline: none;
  border-color: #3498db;
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
}

.range-values {
  margin-top: 8px;
  text-align: center;
  color: #7f8c8d;
  font-size: 0.9rem;
}

.search-input {
  position: relative;
}

.search-input .clear-btn {
  position: absolute;
  right: 12px;
  background: none;
  border: none;
  color: #95a5a6;
  cursor: pointer;
  padding: 4px;
  border-radius: 50%;
}

.search-input .clear-btn:hover {
  background: #f8f9fa;
  color: #e74c3c;
}

.actions {
  display: flex;
  gap: 16px;
  margin-top: 24px;
}

.actions button {
  flex: 1;
  padding: 14px 20px;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  transition: all 0.3s ease;
}

.btn-primary {
  background: linear-gradient(135deg, #3498db, #2980b9);
  color: white;
}

.btn-primary:hover {
  background: linear-gradient(135deg, #2980b9, #1c5d87);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(52, 152, 219, 0.3);
}

.btn-secondary {
  background: white;
  color: #7f8c8d;
  border: 2px solid #e0e6ed !important;
}

.btn-secondary:hover {
  background: #f8f9fa;
  color: #34495e;
}

.stats {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid #e0e6ed;
  text-align: center;
  color: #2c3e50;
}

.stats strong {
  color: #27ae60;
  font-size: 1.2rem;
}

/* Адаптивность */
@media (max-width: 768px) {
  .filters-grid {
    grid-template-columns: 1fr;
  }

  .range-inputs {
    grid-template-columns: 1fr;
  }

  .actions {
    flex-direction: column;
  }

  .filters-container {
    padding: 20px;
  }
}

@media (max-width: 480px) {
  .filters-header h2 {
    font-size: 1.3rem;
  }

  .actions button {
    padding: 12px 16px;
  }
}
</style>