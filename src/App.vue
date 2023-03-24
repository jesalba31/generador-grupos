<template>
  <GeneradorGrupos></GeneradorGrupos>
  <button class="contrast switcher theme-switcher" aria-label="Apagar modo oscuro"><i>Apagar modo oscuro</i></button>
</template>

<script>
import GeneradorGrupos from './components/GeneradorGrupos.vue'

export default {
  name: 'App',
  components: {
    GeneradorGrupos
  }
}
</script>
