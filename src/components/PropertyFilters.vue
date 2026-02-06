<template>
  <section class="filters-container" aria-labelledby="filters-title">
    <header class="filters-header">
      <h2 id="filters-title">🔍 Поиск квартир в Астане</h2>
      <p class="subtitle">Найдите идеальную квартиру с помощью фильтров</p>
    </header>

    <div class="filters-grid">
      <!-- Площадь -->
      <div class="filter-group">
        <label for="area-min">Площадь, м²</label>
        <div class="range-inputs">
          <div class="input-with-icon">
            <i class="pi pi-arrow-right" aria-hidden="true"></i>
            <input
                id="area-min"
                v-model.number="filters.area.min"
                type="number"
                placeholder="От"
                min="0"
                aria-label="Минимальная площадь"
                @input="validateRange('area')"
            />
          </div>
          <div class="input-with-icon">
            <i class="pi pi-arrow-left" aria-hidden="true"></i>
            <input
                id="area-max"
                v-model.number="filters.area.max"
                type="number"
                placeholder="До"
                min="0"
                aria-label="Максимальная площадь"
                @input="validateRange('area')"
            />
          </div>
        </div>
        <div class="range-values">
          <span>{{ areaRangeText }}</span>
        </div>
      </div>

      <!-- Комнаты -->
      <div class="filter-group">
        <label for="rooms-min">Количество комнат</label>
        <div class="range-inputs">
          <div class="input-with-icon">
            <i class="pi pi-home" aria-hidden="true"></i>
            <input
                id="rooms-min"
                v-model.number="filters.rooms.min"
                type="number"
                placeholder="От"
                min="1"
                max="10"
                aria-label="Минимальное количество комнат"
                @input="validateRange('rooms')"
            />
          </div>
          <div class="input-with-icon">
            <i class="pi pi-home" aria-hidden="true"></i>
            <input
                id="rooms-max"
                v-model.number="filters.rooms.max"
                type="number"
                placeholder="До"
                min="1"
                max="10"
                aria-label="Максимальное количество комнат"
                @input="validateRange('rooms')"
            />
          </div>
        </div>
        <div class="range-values">
          <span>{{ roomsRangeText }}</span>
        </div>
      </div>

      <!-- Адрес -->
      <div class="filter-group full-width">
        <label for="address-search">Адрес</label>
        <div class="input-with-icon search-input">
          <i class="pi pi-search" aria-hidden="true"></i>
          <input
              id="address-search"
              v-model.trim="filters.address"
              type="search"
              placeholder="Введите улицу или район..."
              aria-label="Поиск по адресу"
              @input="sanitizeAddress"
          />
          <button
              v-if="filters.address"
              type="button"
              class="clear-btn"
              @click="filters.address = ''"
              aria-label="Очистить поле поиска"
          >
            <i class="pi pi-times" aria-hidden="true"></i>
          </button>
        </div>
      </div>
    </div>

    <!-- Кнопки действий -->
    <div class="actions">
      <button
          type="button"
          class="btn-primary"
          @click="emitFilters"
          aria-label="Применить фильтры"
      >
        <i class="pi pi-search" aria-hidden="true"></i> Найти квартиры
      </button>
      <button
          type="button"
          class="btn-secondary"
          @click="resetFilters"
          aria-label="Сбросить все фильтры"
      >
        <i class="pi pi-refresh" aria-hidden="true"></i> Сбросить фильтры
      </button>
    </div>

    <!-- Статистика -->
    <div v-if="filteredCount > 0" class="stats" aria-live="polite">
      <p>Найдено квартир: <strong>{{ filteredCount }}</strong></p>
    </div>
  </section>
</template>

<script setup>
import { reactive, computed, watch, toRefs } from 'vue'

const props = defineProps({
  filteredCount: {
    type: Number,
    default: 0,
    validator: (value) => value >= 0
  }
})

const emit = defineEmits(['filter-change'])

const filters = reactive({
  area: {
    min: null,
    max: null
  },
  rooms: {
    min: null,
    max: null
  },
  address: ''
})

const areaRangeText = computed(() => {
  const min = filters.area.min || 0
  const max = filters.area.max !== null ? filters.area.max : '∞'
  return `${min} - ${max} м²`
})

const roomsRangeText = computed(() => {
  const min = filters.rooms.min || 1
  const max = filters.rooms.max !== null ? filters.rooms.max : '∞'
  return `${min} - ${max} комнат`
})

const validateRange = (type) => {
  const range = filters[type]
  if (range.min !== null && range.max !== null && range.min > range.max) {
    const temp = range.min
    range.min = range.max
    range.max = temp
  }
}

const sanitizeAddress = () => {
  filters.address = filters.address.replace(/[<>]/g, '')
}

const emitFilters = () => {
  const filtersToEmit = {
    area: { ...filters.area },
    rooms: { ...filters.rooms },
    address: filters.address.toLowerCase().trim()
  }
  emit('filter-change', filtersToEmit)
}

const resetFilters = () => {
  filters.area.min = null
  filters.area.max = null
  filters.rooms.min = null
  filters.rooms.max = null
  filters.address = ''

  emit('filter-change', {
    area: { min: null, max: null },
    rooms: { min: null, max: null },
    address: ''
  })
}

watch(
    () => ({ ...filters }),
    () => {
      emitFilters()
    },
    { deep: true }
)

defineExpose({
  filters: toRefs(filters),
  resetFilters,
  emitFilters
})
</script>

<style scoped>
.filters-container {
  --primary-color: #3498db;
  --primary-dark: #2980b9;
  --secondary-color: #2ecc71;
  --danger-color: #e74c3c;
  --text-primary: #2c3e50;
  --text-secondary: #7f8c8d;
  --border-color: #e0e6ed;
  --background-light: #f8f9fa;

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
  color: var(--text-primary);
  margin: 0 0 8px 0;
  font-size: 1.5rem;
  line-height: 1.3;
}

.subtitle {
  color: var(--text-secondary);
  margin: 0;
  font-size: 0.95rem;
  line-height: 1.4;
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
  color: var(--text-primary);
  font-size: 0.95rem;
  line-height: 1.4;
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
  color: var(--text-secondary);
  pointer-events: none;
}

.input-with-icon input {
  width: 100%;
  padding: 12px 12px 12px 36px;
  border: 2px solid var(--border-color);
  border-radius: 10px;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  background: white;
  font-family: inherit;
  -webkit-appearance: none;
  -moz-appearance: textfield;
}

.input-with-icon input::-webkit-outer-spin-button,
.input-with-icon input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.input-with-icon input:focus {
  outline: 2px solid var(--primary-color);
  outline-offset: 2px;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
}

.range-values {
  margin-top: 8px;
  text-align: center;
  color: var(--text-secondary);
  font-size: 0.9rem;
  line-height: 1.4;
}

.search-input {
  position: relative;
}

.search-input .clear-btn {
  position: absolute;
  right: 12px;
  background: none;
  border: none;
  color: var(--text-secondary);
  cursor: pointer;
  padding: 4px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

.search-input .clear-btn:hover,
.search-input .clear-btn:focus-visible {
  background: var(--background-light);
  color: var(--danger-color);
  outline: none;
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
  font-family: inherit;
  line-height: 1.4;
}

.btn-primary {
  background: linear-gradient(135deg, var(--primary-color), var(--primary-dark));
  color: white;
}

.btn-primary:hover,
.btn-primary:focus-visible {
  background: linear-gradient(135deg, var(--primary-dark), #1c5d87);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(52, 152, 219, 0.3);
  outline: none;
}

.btn-secondary {
  background: white;
  color: var(--text-secondary);
  border: 2px solid var(--border-color) !important;
}

.btn-secondary:hover,
.btn-secondary:focus-visible {
  background: var(--background-light);
  color: var(--text-primary);
  outline: none;
}

.stats {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid var(--border-color);
  text-align: center;
  color: var(--text-primary);
}

.stats strong {
  color: var(--secondary-color);
  font-size: 1.2rem;
}

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
    font-size: 0.95rem;
  }

  .input-with-icon input {
    padding: 10px 10px 10px 32px;
  }

  .input-with-icon i {
    left: 10px;
  }
}

.filters-container:focus-within {
  outline: 2px solid var(--primary-color);
  outline-offset: 2px;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.btn-secondary:active i {
  animation: spin 0.5s ease;
}
</style>