<template>
  <div class="dashboard">
    <div class="header">
      <img alt="Rick and Morty logo" src="../assets/logo.svg" class="logo">
      <SearchBar @search="findCharacter" />
    </div>
    <hr class="line" />
    <div class="panels">
      <button :class="['panel', favMode ? '' : 'active']" @click="() => favMode = false">
        All Characters
      </button>
      <button :class="['panel', favMode ? 'active' : '']" @click="() => favMode = true">
        Favorites
      </button>
    </div>
    <div class="list">
      <RMHeader />
      <div v-if="errorMessage" class="info">{{ errorMessage }}</div>
      <div v-if="isLoading" class="info">Loading...</div>
      <div v-if="!isLoading">
        <CharacterRow v-for="character in charactersList" :key="character.id" :character="character" :favList="favList"
          @add="addCharacter" @remove="removeCharacter" />
        <RMPagination :pages="pages" v-if="pages > 1 && !favMode" @fetchData="fetchPagedData" />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import SearchBar from './SearchBar.vue';
import CharacterRow from './CharacterRow.vue';
import RMPagination from './RMPagination.vue';
import RMHeader from './RMHeader.vue';
import { CharacterType } from '@/types/Character';
import { ref, onMounted, watch, onBeforeMount, defineComponent } from "vue";
import { SearchOptions } from '@/types/SearchOptions';

const charactersList = ref<CharacterType[]>([])
const favList = ref<CharacterType[]>([])
const pages = ref(0)
const favMode = ref<boolean>(false)
const errorMessage = ref('')
const isSearched = ref(false)
const currentEndpoint = ref('https://rickandmortyapi.com/api/character')
const searchObject = ref({ field: SearchOptions.Name, query: '' })
const isLoading = ref(false)

export default defineComponent({
  name: 'RMDashboard',
  components: {
    SearchBar,
    RMHeader,
    CharacterRow,
    RMPagination,
  },
  setup() {
    onBeforeMount(() => {
      const localStorageData = localStorage.getItem("favList");
      if (localStorageData) {
        favList.value = [...JSON.parse(localStorageData)];
      }
    });

    onMounted(() => {
      fetchData(currentEndpoint.value);
    });

    watch(
      favMode,
      () => {
        findCharacter({ ...searchObject.value })
      },
      { deep: true }
    );

    watch(
      favList,
      (newFavList) => {
        localStorage.setItem("favList", JSON.stringify(newFavList));
      },
      { deep: true }
    );

    watch(
      charactersList,
      (newCharactersList) => {
        if (!newCharactersList || newCharactersList.length === 0) {
          errorMessage.value = 'There is nothing here'
        } else errorMessage.value = ''
      },
      { deep: true }
    );

    function addCharacter(character: CharacterType) {
      favList.value.push(character)
    }

    function removeCharacter(id: number) {
      favList.value = favList.value.filter((char) => char.id != id)
      charactersList.value = charactersList.value.filter((char) => char.id != id)
    }

    async function fetchData(endpoint: string, additionalCommands?: () => void) {
      errorMessage.value = ''
      isLoading.value = true
      try {
        const response = await fetch(endpoint);
        const data = await response.json();
        charactersList.value = data.results;
        pages.value = data.info?.pages ?? 0;
        if (data.error) errorMessage.value = data.error
        if (additionalCommands) additionalCommands();
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally {
        isLoading.value = false
      }
    }

    async function fetchPagedData(page: number) {
      const char = isSearched.value ? '&' : '?'
      try {
        const response = await fetch(`${currentEndpoint.value}${char}page=${page}`);
        const data = await response.json();
        charactersList.value = data.results;
        if (data.error) errorMessage.value = data.error
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    }

    async function findCharacter(searchObj: { field: SearchOptions, query: string }) {
      errorMessage.value = ''
      searchObject.value = { ...searchObj }
      currentEndpoint.value = `https://rickandmortyapi.com/api/character/?${searchObj.field.toLowerCase()}=${searchObj.query}`
      if (!favMode.value) {
        fetchData(
          currentEndpoint.value,
          () => isSearched.value = true)
      }

      if (favMode.value) {
        const fieldKey = searchObj.field.toLowerCase() as keyof CharacterType;
        const newList = favList.value.filter((character) => {
          const fieldValue = character[fieldKey];
          if (typeof fieldValue === "string") {
            return fieldValue.toLowerCase().includes(searchObj.query.toLowerCase());
          }
          return false;
        });
        charactersList.value = newList;
      }
    }

    return {
      charactersList,
      favList,
      pages,
      favMode,
      errorMessage,
      isLoading,
      addCharacter,
      removeCharacter,
      findCharacter,
      fetchPagedData
    }
  },
})
</script>

<style scoped lang="scss">
@import "@/styles/variables.scss";

.logo {
  padding: 32px 140px;

  @include lg {
    padding: 20px;
  }
}

.header {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 100%;
  justify-content: flex-start;

  @include lg {
    justify-content: center;
    flex-direction: column;
  }
}

.line {
  border: 1px solid $line-color;
  ;
}

.info {
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 20px 0;
}

.panel {
  background: transparent;
  border: none;
  font-weight: 500;
  line-height: 150%;
  font-size: 16px;
  margin: 24px 0;
  cursor: pointer;
  color: $primary-color;

  &:first-child {
    margin-right: 80px;
    margin-left: 140px;
  }

  @include lg {
    margin: 20px 0;

    &:first-child {
      margin-right: 40px;
      margin-left: 20px;
    }
  }


}

.active {
  color: $secondary-color;
  border-bottom: 3px solid $secondary-color;
}
</style>
