<script lang="ts">
import { defineComponent, inject, PropType } from 'vue'
import { Champion, Player } from '../App.vue'

export default defineComponent({
  name: 'Players',
  setup() {
    const active = inject('active')
    const updateActive: any = inject('updateActive')
    return { active, updateActive }
  },
  props: {
    players: Array as PropType<Player[]>,
    currentVersion: String
  },
  methods: {
    activate(player: Player) {
      if (this.active) {
        if (this.typeGuard(this.active)) {
          // If active is of Player type
          console.log("is player")

          this.updateActive(player)
        } else {
          // If active is not of Player type
          console.log("is not player")
        }
      } else {
        this.updateActive(player)
      }
    },
    /**
     * Check if object is of Player type
     * @param {any} object Any object
     * @returns {object} Champion object 
     */
    typeGuard(object: any): object is Player {
      return 'champion' in object
    }
  }
})
</script>
<template lang="pug">
div 
  .player(
    v-for='player in players',
    @click='activate(player)',
    :class='active === player ? "active" : ""'
  ) 
    .champion-img {{ currentVersion }}
    .player-name {{ player.name }}
</template>
<style lang="scss" scoped>
@import '../styles/_globals.scss';
.champion-img {
  & > div {
    background-color: $primary-light;
    outline: $light solid 3px;
  }
}
</style>
