<template lang="pug">
.champion-grid-wrapper
  .search 
    input(v-model='searchText')
  .container
    .champion-grid
      .champion(
        v-for='champ in filterIt',
        :key='champ.key',
        @click='activate(champ)',
        :class='{ used: champ.used, active: active === champ }'
      )
        .champion-img
          img(
            :src='"https://ddragon.leagueoflegends.com/cdn/" + currentVersion + "/img/champion/" + champ.image'
          )
        .champion-name {{ champ.name }}
</template>
<script lang="ts">
import { defineComponent, inject, PropType } from 'vue'
import { Champion, Player } from '../App.vue'

export default defineComponent({
  name: 'ChampionGrid',
  components: {},
  props: {
    /**
     * Champion[] containing champions
     * @type Champion[]
     * @required true
     */
    champions: {
      type: Array as PropType<Champion[]>,
      required: true
    },
    /**
     * Current version of League of Legends
     * @type String
     * @required true
     */
    currentVersion: {
      type: String,
      required: true
    }
  },
  setup() {
    const active: any = inject('active')
    const updateActive: any = inject('updateActive')
    return {
      active,
      updateActive
    }
  },
  data() {
    return {
      searchText: ''
    }
  },
  methods: {
    activate(champion: Champion) {
      if (this.active) {
        // if selected
        if (!champion.used) {
          // if champ not used
          if (this.active === champion) {
            this.updateActive(null)
          } else if (this.isPlayer(this.active)) {
            if (this.active.champion) {
              this.active.champion.used = false
            }
            champion.used = true // Set Champion used to true
            this.active.champion = champion
            this.updateActive(null)
          } else {
            console.assert(
              this.active.champion.used === false,
              'FUCKING SHIT REWRITE THIS SHIT'
            )
            this.updateActive(champion)
          }
        }
      } else if (!champion.used) {
        this.updateActive(champion)
      }
    },
    // active === champ || champ.used ? updateActive(null) : updateActive(champ)
    isPlayer(object: any): object is Player {
      return 'champion' in object
    }
  },
  computed: {
    /**
     * Returns the champions matching the search query
     * @type Champion[] | undefined
     */
    filterIt(): Champion[] | undefined {
      let q: string = this.searchText.toLowerCase().trim()
      if (q === '') {
        return this.champions
      } else {
        return this.champions?.filter((champ) => {
          return champ.name.toLowerCase().includes(q)
        })
      }
    }
  }
})
</script>
<style lang="scss" scoped>
@import '../styles/_globals.scss';
.container {
  height: 90%;
  width: 100%;
  box-sizing: border-box;
}
.champion-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(95px, 1fr));
  max-height: 100%;
  box-sizing: border-box;
  padding: 3px 0;
  gap: 3px 0;
  overflow-y: scroll;
  &::-webkit-scrollbar {
    width: 10px;
  }
  &::-webkit-scrollbar-track {
    background: transparent;
  }
  &::-webkit-scrollbar-thumb {
    background: $primary;
    border-radius: 15px;
  }

  .champion {
    font-size: 0.9em;
    color: #fff;
    cursor: pointer;
    .champion-name {
      font-size: small;
      margin-top: 3px;
    }
    &:hover {
      img {
        box-shadow: 0 0 3pt $light;
      }
    }
  }
}
.used {
  cursor: default;
  img {
    filter: grayscale(100%);
  }
}
.active {
  box-sizing: content-box;
  .champion-img {
    outline: $light solid 3px;
  }
}
</style>