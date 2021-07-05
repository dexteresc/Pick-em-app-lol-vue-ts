<template lang="pug">
.champion-grid
  .champion(v-for='champ in champions', :key='champ')
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
    },
    searchText: String
  },
  setup() {
    onUpdated(() => {})
  },
  methods: {
  }
})
</script>
<style lang="scss" scoped>
@import '../styles/_globals.scss';
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
    display: flex;
    flex-flow: column;
    align-items: center;
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