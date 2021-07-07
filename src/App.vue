<template lang="pug">
.pick-em-wrapper
  header
    Side.blue-side(:name='side.blueSide.name')
      Bans(:bans='side.blueSide.bans')
    //
    Side.red-side(:name='side.redSide.name')
      Bans(:bans='side.redSide.bans')
  main
    Players.blue-side(
      :players='side.blueSide.players',
      :currentVersion='currentVersion'
    )
    ChampionGrid.champion-grid-wrapper(
      :champions='champions',
      :currentVersion='currentVersion'
    )
    Players.red-side(
      :players='side.redSide.players',
      :currentVersion='currentVersion'
    )
  footer
    p [Your product] isn't endorsed by Riot Games and doesn't reflect the views or opinions of Riot Games or anyone officially involved in producing or managing Riot Games properties. Riot Games, and all associated properties are trademarks or registered trademarks of Riot Games, Inc.
</template>

<script lang="ts">
import axios from 'axios'
import { defineComponent, provide, ref } from 'vue'
import { ChampionGrid, Bans, Players, Side } from './components'

/**
 * Champion inteface
 * @typedef {object} Champion
 */
export interface Champion {
  /** Name of the champion */
  name: string
  /** Champion key */
  key: number
  /**
   * Champion image address
   * @example "Aatrox.png"
   */
  image: string
  /**
   * Champion tags
   * @example ["Support", "Tank"]
   */
  tags?: string[]
}

/**
 * Interface for player
 * @typedef {object} Player
 */
export interface Player {
  /** Player name */
  name: string
  /** Players selected champion */
  champion: Champion | null // Think null is ok here, (undefined?)
}

/**
 * App component
 * @displayName App
 */
export default defineComponent({
  name: 'App',
  components: { ChampionGrid, Bans, Players, Side },
  setup() {
    const active = ref<Champion | Player | null>(null)
    /**
     * Update the active/selected champion or player
     * @param {Champion} champion Champion or null
     */
    const updateActive = (champion: Champion | Player | null) => {
      if (champion) {
        console.log(champion)
        active.value = champion
      } else {
        active.value = null
      }
    }

    provide('active', active)
    provide('updateActive', updateActive)
  },
  /**
   * Instanciate currentVersion and champions at app start.
   * Values won't change until reload. (Intentional)
   */
  async created() {
    const currentVersion = await axios
      .get('https://ddragon.leagueoflegends.com/api/versions.json')
      .then((res) => {
        return res.data[0]
      })
    const champions = await axios
      .get(
        `https://ddragon.leagueoflegends.com/cdn/${currentVersion}/data/en_US/champion.json`
      )
      .then((res) => {
        let arr: any = []
        for (const champ in res.data.data) {
          if (Object.prototype.hasOwnProperty.call(res.data.data, champ)) {
            const element = res.data.data[champ]
            const champion: Champion = {
              name: element.name,
              key: element.key,
              image: element.image.full,
              tags: element.tags
            }
            arr.push(champion)
          }
        }
        console.dir(arr)
        return arr
      })
      .catch((e) => console.error(e))

    this.currentVersion = currentVersion
    this.champions = champions

    this.side.redSide.players = [
      {
        name: 'r1',
        champion: null
      },
      {
        name: 'r2',
        champion: null
      },
      {
        name: 'r3',
        champion: null
      },
      {
        name: 'r4',
        champion: null
      },
      {
        name: 'r5',
        champion: null
      }
    ]
  },
  data() {
    return {
      /**
       * Each sides respective data
       * @property {string} name The teams name
       * @property {players}
       */
      side: {
        blueSide: {
          name: 'Team 1',
          players: [] as Player[],
          bans: [1, 2, 3, 4, 5]
        },
        redSide: {
          name: 'Team 2',
          players: [] as Player[],
          bans: [1, 2, 3, 4, 5]
        }
      },
      currentVersion: '' as string,

      champions: [] as Champion[]
    }
  }
})
</script>

<style lang="scss">
@import './styles/_globals.scss';
html,
body {
  margin: 0;
  background-color: $primary;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.pick-em-wrapper {
  width: 90%;
  margin: auto auto;
  display: flex;
  flex-flow: column;
  justify-content: space-around;
  height: 100vh;
}
header {
  display: flex;
  flex-flow: row;
  justify-content: space-between;
}
main {
  display: flex;
  flex-flow: row;
  height: 80vh;
  .champion-grid-wrapper {
    flex: 1 1 40%;
  }
  .blue,
  .red {
    flex: 0 0 auto;
  }
}
.container {
  width: 100%;
  background-color: $primary-light;
  padding: 0.8em 0.4em;
  border-radius: 15px;
}
</style>
