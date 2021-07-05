<template lang="pug">
.pick-em-wrapper
  header
    Side.blue-side(:name='side.blueSide.name')
      Bans(:bans='side.blueSide.bans')
    //
    Side.red-side(:name='side.redSide.name')
      Bans(:bans='side.redSide.bans')
  main
    Players
    .container
      SearchBar(@input='filterIt.set')
      ChampionGrid.champion-grid(
        :champions='champions',
        :currentVersion='currentVersion'
      )
    Players
  footer
</template>

<script lang="ts">
import axios from 'axios'
import { defineComponent } from 'vue'
import { ChampionGrid, Bans, Players, SearchBar, Side } from './components/'

export default defineComponent({
  name: 'App',
  components: { ChampionGrid, Bans, Players, SearchBar, Side },
  created() {
    this.currentVersion = currentVersion
    this.champions = champions
  },
  data() {
    return nonReactiveData({
      // reactive
      side: {
        blueSide: {
          name: 'Team 1',
          players: [
            {
              name: 'b1',
              champion: {}
            },
            {
              name: 'b2',
              champion: {}
            },
            {
              name: 'b3',
              champion: {}
            },
            {
              name: 'b4',
              champion: {}
            },
            {
              name: 'b5',
              champion: {}
            }
          ]
        },
        redSide: {
          name: 'Team 2',
          players: [
            {
              name: 'r1',
              champion: {}
            },
            {
              name: 'r2',
              champion: {}
            },
            {
              name: 'r3',
              champion: {}
            },
            {
              name: 'r4',
              champion: {}
            },
            {
              name: 'r5',
              champion: {}
            }
          ]
        }
      },
      searchText: ''
    })<{
      // Non reactive data
      currentVersion: ''
      champions: []
    }>()
  },
  beforeMount() {},
  methods: {
    getChampions(): object {
      return champions
    },
    getVersion(): any {
      return currentVersion
    }
  },
  computed: {
    filterIt: {
      get(): object[] {
        let q = this.searchText.toLowerCase().trim()
        if (q === '') {
          return this.champions
        }
        let results: object[] = []
        for (const key in this.champions) {
          if (Object.hasOwnProperty.call(this.champions, key)) {
            if (
              this.champions[key].name.toLowerCase().includes(q) ||
              this.champions[key].tags.includes(this.searchText.trim())
            ) {
              results.push(this.champions[key])
            }
          }
        }
        return results
      },
      set(q: string) {
        this.searchText = q
      }
    }
  }
})
const currentVersion = await axios
  .get('https://ddragon.leagueoflegends.com/api/versions.json')
  .then((res) => {
    console.log(res)
    return res.data[0]
  })
const champions = await axios
  .get(
    `https://ddragon.leagueoflegends.com/cdn/${currentVersion}/data/en_US/champion.json`
  )
  .then((res) => {
    console.log(res.data.data)
    return res.data.data
  })
  .catch((e) => console.error(e))

// helper
function nonReactiveData<TData>(data: TData) {
  return <TNonReactiveData>() => data as TData & TNonReactiveData
}
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
  .champion-grid {
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
