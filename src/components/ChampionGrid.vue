<template lang="pug">
.champion-grid-pick-em-wrapper
  .search 
    input(v-model='searchText')
  .container
    .champion-grid
      .champion(
        v-for='champ in filterIt',
        :key='champ.key',
        @click='active === champ ? updateActive(null) : updateActive(champ)',
        :class='active === champ ? "active" : ""'
      )
        .champion-img
          img(
            :src='"https://ddragon.leagueoflegends.com/cdn/" + currentVersion + "/img/champion/" + champ.image'
          )
        .champion-name {{ champ.name }}
</template>
<script lang="ts">
import { defineComponent, inject, PropType } from 'vue'
import { Champion } from '../App.vue'

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
    const active = inject('active')
    const updateActive = inject('updateActive')
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
  methods: {},
  computed: {
    /**
     * Returns the champions matching the search query
     * @type Champion[] | undefined
     */
    filterIt(): Champion[] | undefined {
      let q: string = this.searchText.toLowerCase().trim()
      if (q === '' || /\d/.test(q)) {
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
<style lang="scss">
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

    &.active {
      box-sizing: content-box;
      img {
        outline: $light solid 3px;
      }
    }
    &:hover {
      img {
        box-shadow: 0 0 3pt $light;
      }
    }
  }
}
.champion-img {
  display: block;
  background-color: $primary-light;
  width: 80px;
  height: 80px;
  margin: auto;

  img {
    width: 100%;
    height: 100%;
  }
}
</style>