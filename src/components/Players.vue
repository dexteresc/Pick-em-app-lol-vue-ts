<script lang="ts">
import { defineComponent, inject, PropType } from 'vue'
import { Champion, Player } from '../App.vue'

export default defineComponent({
  name: 'Players',
  setup() {
    const active: any = inject('active')
    const updateActive: any = inject('updateActive')
    return { active, updateActive }
  },
  props: {
    players: Array as PropType<Player[]>,
    currentVersion: String
  },
  methods: {
    activate(player: Player): void {
      if (this.active) {
        if (this.active === player) {
          this.updateActive(null)
        } else {
          if (this.isChampion(this.active)) {
            // if active is champion
            this.active.used = true
            if (player.champion) {
              player.champion.used = false
            }
            player.champion = this.active
            this.updateActive(null)
          } else {
            // if active is player
            if (player.champion || this.active.champion) {
              // if player has champion or active has champion
              let playerChamp = player.champion || null
              player.champion = this.active.champion
              this.active.champion = playerChamp
              this.updateActive(null)
            } else {
              this.updateActive(player)
            }
          }
        }
      } else {
        this.updateActive(player)
      }
    },
    /**
     * Check if object is of Champion type
     * @param {any} object Any object
     * @returns {number} Champion key
     */
    isChampion(object: any): object is Champion {
      return 'key' in object
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
    .champion-img
      img(
        v-if='player.champion',
        :src='"https://ddragon.leagueoflegends.com/cdn/" + currentVersion + "/img/champion/" + player.champion.image'
      )
    .player-name {{ player.name.toUpperCase() }}
</template>
<style lang="scss" scoped>
@import '../styles/_globals.scss';

.blue-side {
  .player {
    flex-flow: row-reverse;

    &.active {
      background-color: $blue;
      color: $light;
    }
    .player-name {
      margin-right: 0.5em;
    }
  }
}
.red-side {
  .player {
    flex-flow: row;
    &.active {
      background-color: $red;
      color: $light;
    }
    .player-name {
      margin-left: 0.5em;
    }
  }
}
.player {
  display: flex;
  padding: 0.5em;
  margin: 0.2em;
  align-items: center;
  border-radius: 10px;
  background-color: $primary-light;
  color: $light;
  .champion-img {
    background-color: darken($primary-light, $amount: 8);
  }
}
.champion-img {
  cursor: pointer;
  & > div {
    background-color: $primary-light;
    outline: $light solid 3px;
  }
}
</style>
