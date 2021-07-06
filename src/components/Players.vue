<script lang="ts">
import { defineComponent, PropType } from 'vue'
import { Champion, Player } from '../App.vue'

export default defineComponent({
  name: 'Players',
  setup() {},
  inject: ['currentVersion'],
  props: {
    players: Array as PropType<Player[]>
  }
})
</script>
<template lang="pug">
div 
  .player(v-for='player in players') 
    .champion-img
      img(
        :src='player.champion"https://ddragon.leagueoflegends.com/cdn/" + currentVersion + "/img/champion/" + player.image'
      )
    .player-name {{ player.name }}
</template>
<style lang="scss" scoped>
</style>
