<template>
  <div class="searchbar">
    <span class="searchBy">Search by</span>
    <button class="selectContainer" @click="expand()">
      <div class="select">{{ searchObj.field }} <img src="../assets/arrow-select.svg" /></div>
      <div v-if="showOptions" class="options">
        <div v-for="(option, index) in searchOptions" :key="index" class="option" @click="changeOption(option)">{{ option
        }}</div>
      </div>
    </button>
    <span class=input>
      <input class="magnifier" v-model="searchObj.query" @keydown.enter="searchCharacter()" />
      <button class="button" @click="searchCharacter()">
        <img src="../assets/magnifier.svg" />
      </button>
    </span>
  </div>
</template>

<script lang="ts">
import { SearchOptions } from '@/types/SearchOptions';
import { SetupContext, ref, defineComponent } from 'vue';

interface SearchBarEvents {
  search: (searchObj: { field: SearchOptions, query: string }) => void;
}

export default defineComponent({
  name: 'SearchBar',
  emits: ['search'],
  setup(_props: unknown, { emit }: SetupContext<SearchBarEvents>) {
    const searchOptions = SearchOptions;
    const showOptions = ref(false)
    const searchObj = ref({ query: '', field: SearchOptions.Name })

    function expand() {
      showOptions.value = !showOptions.value
    }

    function changeOption(option: SearchOptions) {
      searchObj.value.field = option
    }

    function searchCharacter() {
      emit("search", searchObj.value)
    }

    return {
      searchOptions,
      showOptions,
      searchObj,
      expand,
      changeOption,
      searchCharacter
    }
  }
})
</script>

<style scoped lang="scss">
@import "@/styles/variables.scss";

.searchbar {
  display: flex;
  width: 505px;
  height: 56px;
  flex-shrink: 0;
  border-radius: 12px;
  border: 1px solid $primary-color;
  background: #fff;

  @include lg {
    width: 90%;
    height: 40px;
  }
}

.searchBy {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 123px;
  border-right: 1px solid $primary-color;
  padding: 16px 20px;
  box-sizing: border-box;
  font-size: 16px;

  @include lg {
    padding: 10px;
    font-size: 10px;
  }

}

.selectContainer {
  position: relative;
  background-color: #fff;
  border: none;
  width: 128px;
  border-right: 1px solid $primary-color;
  padding: 16px;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
  color: $primary-color;
  font-size: 16px;

  img {
    width: 10px;
    height: 10px;
  }

  @include lg {
    padding: 10px;
    font-size: 10px;
    width: 30%;

    img {
      width: 5px;
      height: 5px;
    }
  }


  .select {
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
  }

  .options {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    position: absolute;
    top: 100%;
    left: -1px;
    width: 127px;
    border: 1px solid $primary-color;
    border-radius: 0px 0px 12px 12px;
    background-color: #fff;

    .option {
      display: flex;
      font-family: 'Poppins', sans-serif;
      width: 100%;
      color: $primary-color;
      font-size: 16px;
      padding: 16px 18px;
      border-bottom: 1px solid $primary-color;
      box-sizing: border-box;

      &:hover {
        background-color: $primary-color-hover;
      }

      &:last-child {
        border-radius: 0px 0px 12px 12px;
        border-bottom: none;
      }

      @include lg {
        font-size: 10px;
        padding: 10px;
      }
    }
  }
}

.input {
  display: flex;
  align-items: center;
  padding: 12px;
  box-sizing: border-box;
  width: 252px;

  input {
    background-color: transparent;
    border: none;
    padding: 16px 0;
    font-size: 16px;
    color: $primary-color;
    width: 204px;
    box-sizing: border-box;

    &:focus {
      outline: none;
    }

    @include lg {
      width: 100%;
      font-size: 10px;

      input {
        font-size: 10px;
      }
    }
  }

  .button {
    background-color: transparent;
    border: none;
    cursor: pointer;

    img {
      width: 32px;
      height: 32px;
    }

    @include lg {
      img {
        width: 100%;
        height: 100%;
      }
    }
  }
}
</style>
