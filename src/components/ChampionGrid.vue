<template lang="pug">
.champion-grid-pick-em-wrapper
  .search 
    input(v-model='searchText')
  .container
    .champion-grid
      .champion(v-for='champ in champions', :key='champ.key')
        .champion-img
          img(
            :src='"https://ddragon.leagueoflegends.com/cdn/" + currentVersion + "/img/champion/" + champ.image.full'
          )
        .champion-name {{ champ.name }}
</template>
<script lang="ts">
import { defineComponent, onUpdated } from 'vue'

export default defineComponent({
  name: 'ChampionGrid',
  props: {
    champions: {
      type: Object,
      required: true
    },
    currentVersion: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      searchText: ''
    }
  },
  setup() {
    onUpdated(() => {})
  },
  methods: {},
  computed: {
    filterIt(): object {
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
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  height: 100%;
  box-sizing: border-box;
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
    img {
      margin-bottom: 3px;
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
  img {
    width: 80px;
    height: 80px;
  }
}
</style>