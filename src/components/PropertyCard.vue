<template>
  <div class="property-card">
    <div class="card-image">
      <img :src="property.image" :alt="property.address" loading="lazy" />
      <div class="card-badge">
        {{ property.rooms }} комн.
      </div>
      <div class="price-tag">
        {{ formatPrice(property.price) }}
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
        <button class="btn-view" @click="viewDetails">
          <i class="pi pi-eye"></i> Подробнее
        </button>
        <button class="btn-contact">
          <i class="pi pi-phone"></i> Позвонить
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  property: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['view-details'])

const formatPrice = (price) => {
  return new Intl.NumberFormat('ru-RU').format(price) + ' ₸'
}

const viewDetails = () => {
  emit('view-details', props.property)
}
</script>

<style scoped>
.property-card {
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
  background: rgba(52, 152, 219, 0.95);
  color: white;
  padding: 6px 14px;
  border-radius: 20px;
  font-weight: 600;
  font-size: 0.9rem;
  backdrop-filter: blur(4px);
}

.price-tag {
  position: absolute;
  bottom: 16px;
  right: 16px;
  background: rgba(231, 76, 60, 0.95);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-weight: 700;
  font-size: 1.1rem;
  backdrop-filter: blur(4px);
}

.card-content {
  padding: 24px;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
}

.card-address {
  margin: 0 0 16px 0;
  color: #2c3e50;
  font-size: 1.2rem;
  line-height: 1.4;
}

.card-features {
  display: flex;
  gap: 24px;
  margin-bottom: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid #e0e6ed;
}

.feature {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #7f8c8d;
}

.feature i {
  color: #3498db;
}

.card-description {
  color: #5d6d7e;
  line-height: 1.6;
  margin-bottom: 24px;
  flex-grow: 1;
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
}

.btn-view {
  background: #f8f9fa;
  color: #3498db;
  border: 2px solid #e0e6ed !important;
}

.btn-view:hover {
  background: #3498db;
  color: white;
  border-color: #3498db !important;
}

.btn-contact {
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  color: white;
}

.btn-contact:hover {
  background: linear-gradient(135deg, #27ae60, #219653);
  transform: translateY(-2px);
}

/* Адаптивность */
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
</style>