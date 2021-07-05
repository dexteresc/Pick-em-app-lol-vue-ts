<script lang="ts">
import { defineComponent, onUpdated, ref } from 'vue'

export default defineComponent({
  name: 'SearchBar',
  setup() {
    const searchText = ref('')
    onUpdated(() => {
    
    })
    return { searchText }
  },
  methods: {}
})
</script>
<template lang="pug">
input(v-model='searchText')
</template>