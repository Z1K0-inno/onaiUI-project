<template>
  <article class="property-card">
    <div class="card-image">
      <img
          :src="property.image"
          :alt="`Квартира по адресу: ${property.address}`"
          loading="lazy"
      />
      <div class="card-badge">
        {{ property.rooms }} комн.
      </div>
      <div class="price-tag">
        {{ formattedPrice }}
      </div>
    </div>

    <div class="card-content">
      <h3 class="card-address">{{ property.address }}</h3>

      <div class="card-features">
        <div class="feature">
          <i class="pi pi-ruler"></i>
          <span>{{ property.area }} м²</span>
        </div>
        <div class="feature">
          <i class="pi pi-home"></i>
          <span>{{ property.rooms }} комнат</span>
        </div>
      </div>

      <p class="card-description">{{ property.description }}</p>

      <div class="card-actions">
        <button
            class="btn-view"
            @click="handleViewDetails"
            aria-label="Посмотреть подробности о квартире"
        >
          <i class="pi pi-eye"></i> Подробнее
        </button>
        <button
            class="btn-contact"
            aria-label="Позвонить по поводу квартиры"
        >
          <i class="pi pi-phone"></i> Позвонить
        </button>
      </div>
    </div>
  </article>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  property: {
    type: Object,
    required: true,
    validator(value) {
      return (
          typeof value.address === 'string' &&
          typeof value.area === 'number' &&
          typeof value.rooms === 'number' &&
          typeof value.price === 'number' &&
          typeof value.image === 'string' &&
          typeof value.description === 'string'
      )
    }
  }
})

const emit = defineEmits(['view-details'])

const formattedPrice = computed(() => {
  const formatter = new Intl.NumberFormat('ru-RU', {
    style: 'currency',
    currency: 'KZT',
    minimumFractionDigits: 0,
    maximumFractionDigits: 0
  })
  return formatter.format(props.property.price)
})

const handleViewDetails = () => {
  emit('view-details', {
    id: props.property.id || Date.now(),
    ...props.property
  })
}
</script>

<style scoped>
.property-card {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --danger-color: #e74c3c;
  --text-primary: #2c3e50;
  --text-secondary: #7f8c8d;
  --border-color: #e0e6ed;
  --background-light: #f8f9fa;

  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.property-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 12px 30px rgba(0, 0, 0, 0.15);
}

.card-image {
  position: relative;
  height: 220px;
  overflow: hidden;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.property-card:hover .card-image img {
  transform: scale(1.05);
}

.card-badge {
  position: absolute;
  top: 16px;
  left: 16px;
  background: rgba(var(--primary-color-rgb, 52, 152, 219), 0.95);
  color: white;
  padding: 6px 14px;
  border-radius: 20px;
  font-weight: 600;
  font-size: 0.9rem;
  backdrop-filter: blur(4px);
  z-index: 1;
}

.price-tag {
  position: absolute;
  bottom: 16px;
  right: 16px;
  background: rgba(var(--danger-color-rgb, 231, 76, 60), 0.95);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-weight: 700;
  font-size: 1.1rem;
  backdrop-filter: blur(4px);
  z-index: 1;
}

.card-content {
  padding: 24px;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
}

.card-address {
  margin: 0 0 16px 0;
  color: var(--text-primary);
  font-size: 1.2rem;
  line-height: 1.4;
}

.card-features {
  display: flex;
  gap: 24px;
  margin-bottom: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--border-color);
}

.feature {
  display: flex;
  align-items: center;
  gap: 8px;
  color: var(--text-secondary);
}

.feature i {
  color: var(--primary-color);
}

.card-description {
  color: #5d6d7e;
  line-height: 1.6;
  margin-bottom: 24px;
  flex-grow: 1;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.card-actions {
  display: flex;
  gap: 12px;
  margin-top: auto;
}

.card-actions button {
  flex: 1;
  padding: 12px 20px;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s ease;
  font-family: inherit;
}

.btn-view {
  background: var(--background-light);
  color: var(--primary-color);
  border: 2px solid var(--border-color) !important;
}

.btn-view:hover,
.btn-view:focus-visible {
  background: var(--primary-color);
  color: white;
  border-color: var(--primary-color) !important;
  outline: none;
}

.btn-contact {
  background: linear-gradient(135deg, var(--secondary-color), #27ae60);
  color: white;
}

.btn-contact:hover,
.btn-contact:focus-visible {
  background: linear-gradient(135deg, #27ae60, #219653);
  transform: translateY(-2px);
  outline: none;
}

@media (max-width: 768px) {
  .card-image {
    height: 180px;
  }

  .card-content {
    padding: 20px;
  }

  .card-address {
    font-size: 1.1rem;
  }

  .card-actions {
    flex-direction: column;
  }
}

@media (max-width: 480px) {
  .property-card {
    margin: 0 -16px;
    border-radius: 0;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  }

  .property-card:hover {
    transform: none;
  }

  .card-features {
    flex-direction: column;
    gap: 12px;
  }
}

.property-card:focus-within {
  outline: 2px solid var(--primary-color);
  outline-offset: 2px;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.card-image {
  background: linear-gradient(110deg, #ececec 8%, #f5f5f5 18%, #ececec 33%);
  background-size: 200% 100%;
  animation: 1.5s shine linear infinite;
}

@keyframes shine {
  to {
    background-position-x: -200%;
  }
}
</style>