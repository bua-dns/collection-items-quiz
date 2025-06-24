<script setup>
import { onMounted, ref } from 'vue'
import L from 'leaflet'

const mapRef = ref(null)

const props = defineProps({
  lat: {
    type: Number,
    required: true,
  },
  lng: {
    type: Number,
    required: true,
  },
  label: {
    type: String,
    default: 'POI',
  }
})

onMounted(() => {
  const map = L.map(mapRef.value).setView([props.lat, props.lng], 12)

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(map)

  L.marker([props.lat, props.lng])
    .addTo(map)
    .bindPopup(props.label)
    .openPopup()
})
</script>

<template>
  <div id="map" ref="mapRef" style="height: 400px; width: 100%; border-radius: 8px;"></div>
</template>

<style scoped lang="scss">
#map {
    height: 20rem;
    width: 100%;
}
</style>
