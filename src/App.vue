<template lang="pug">
.pick-em-wrapper
  header
    Side.blue-side(:name='side.blueSide.name')
      Bans(:bans='side.blueSide.bans')
    //
    Side.red-side(:name='side.redSide.name')
      Bans(:bans='side.redSide.bans')
  main
    Players.blue-side(:players='side.blueSide.players')
    ChampionGrid.champion-grid-wrapper(:champions='champions')
    Players
  footer
</template>

<script lang="ts">
import axios from 'axios'
import { defineComponent } from 'vue'
import { ChampionGrid, Bans, Players, Side } from './components'

/**
 * Interface for champion
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
  champion: Champion
}

/**
 * App component
 * @displayName App
 */
export default defineComponent({
  name: 'App',
  components: { ChampionGrid, Bans, Players, Side },
  /**
   * Instanciate currentVersion and champions at app start.
   * Values won't change until reload. (Intentional)
   */
  async created() {
    this.currentVersion = await axios
      .get('https://ddragon.leagueoflegends.com/api/versions.json')
      .then((res) => {
        return res.data[0]
      })
    this.champions = await axios
      .get(
        `https://ddragon.leagueoflegends.com/cdn/${this.currentVersion}/data/en_US/champion.json`
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
    console.log(this.currentVersion)
    this.side.redSide.players = [
      {
        name: 'r1',
        champion: this.noChamp
      },
      {
        name: 'r2',
        champion: this.noChamp
      },
      {
        name: 'r3',
        champion: this.noChamp
      },
      {
        name: 'r4',
        champion: this.noChamp
      },
      {
        name: 'r5',
        champion: this.noChamp
      }
    ]

    console.log(this.side.blueSide.players)
  },
  setup() {
    const noChamp: Champion = {
      name: '',
      image: '',
      key: 0
    }
    return { noChamp }
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

      champions: []
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
