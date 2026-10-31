<script setup lang="ts">
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const props = defineProps<{
  now: Date
  showSeconds: boolean
}>()

const { locale } = useI18n()
const seconds = computed(() => props.now.getSeconds())
const minutes = computed(() => props.now.getMinutes() + seconds.value / 60)
const hours = computed(() => props.now.getHours() % 12 + minutes.value / 60)
const timeLabel = computed(() => props.now.toLocaleTimeString(locale.value, {
  hour: 'numeric',
  minute: '2-digit',
  second: props.showSeconds ? '2-digit' : undefined,
}))

const hourMarkers = Array.from({ length: 12 }, (_, index) => {
  const hour = index + 1
  const angle = hour * Math.PI / 6
  return { hour, x: 160 + Math.sin(angle) * 112, y: 160 - Math.cos(angle) * 112 }
})
</script>

<template>
  <svg class="analog-clock" viewBox="0 0 320 320" role="img" :aria-label="timeLabel">
    <circle cx="160" cy="160" r="153" fill="none" stroke="currentColor" stroke-opacity="0.2" />
    <g stroke="currentColor" stroke-linecap="round">
      <line
        v-for="tick in 60" :key="tick"
        x1="160" y1="19" x2="160" :y2="tick % 5 === 0 ? 32 : 24"
        :stroke-width="tick % 5 === 0 ? 3 : 1"
        :opacity="tick % 5 === 0 ? 0.9 : 0.35"
        :transform="`rotate(${tick * 6} 160 160)`"
      />
    </g>
    <g fill="currentColor" text-anchor="middle" font-size="20" font-weight="500">
      <text v-for="marker in hourMarkers" :key="marker.hour" :x="marker.x" :y="marker.y + 7">
        {{ marker.hour }}
      </text>
    </g>
    <g stroke="currentColor" stroke-linecap="round">
      <line class="hour-hand" x1="160" y1="171" x2="160" y2="86" stroke-width="8" :transform="`rotate(${hours * 30} 160 160)`" />
      <line class="minute-hand" x1="160" y1="174" x2="160" y2="56" stroke-width="5" :transform="`rotate(${minutes * 6} 160 160)`" />
      <line v-if="showSeconds" class="second-hand" x1="160" y1="184" x2="160" y2="39" stroke-width="1.5" opacity="0.65" :transform="`rotate(${seconds * 6} 160 160)`" />
    </g>
    <circle cx="160" cy="160" r="6" fill="currentColor" />
  </svg>
</template>

<style scoped>
.analog-clock {
  display: block;
  width: 100%;
  height: 100%;
  font-family: 'SFCompactRounded', 'Huninn', sans-serif;
}
</style>
