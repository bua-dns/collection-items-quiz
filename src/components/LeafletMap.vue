<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, watch, ref } from 'vue'
import L from 'leaflet'

const props = defineProps({
  latitude: Number,
  longitude: Number,
  zoom: { type: Number, default: 13 }
})

const mapContainer = ref(null)
let mapInstance = null
let marker = null

onMounted(() => {
  if (!mapContainer.value || mapInstance) return

  mapInstance = L.map(mapContainer.value, {
    center: [props.latitude, props.longitude],
    zoom: props.zoom
  })

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(mapInstance)

  marker = L.marker([props.latitude, props.longitude]).addTo(mapInstance)
})

watch(() => [props.latitude, props.longitude], ([lat, lng]) => {
  if (mapInstance) {
    mapInstance.setView([lat, lng], props.zoom)
    if (marker) {
      marker.setLatLng([lat, lng])
    } else {
      marker = L.marker([lat, lng]).addTo(mapInstance)
    }
  }
})

onBeforeUnmount(() => {
  if (mapInstance) {
    mapInstance.remove()
    mapInstance = null
  }
})
</script>

<style scoped>
.map-container {
  height: 200px;
  width: 100%;
  margin-top: 1em;
  border: 1px solid #ccc;
  border-radius: 8px;
}
</style>
