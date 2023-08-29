<template>
  <div class="characterRow">
    <span class="colImg">
      <span class="characterImg">
        <img :class="[props.character.status === 'Dead' ? 'dead' : '']" :src="props.character.image" />
        <span v-if="props.character.status === 'Dead'" class="ribbon"><img src='../assets/ribbon.svg'></span>
      </span>
    </span>
    <span class="col">{{ props.character.id }}</span>
    <span class="colName">{{ character.name }}</span>
    <span class="col"><img :src="getGenderIcon(character.gender)" />{{ character.gender }}</span>
    <span class="col">{{ character.species }}</span>
    <span class="col">{{ episodeInfo }}</span>
    <span class="colInfo">
      <span><b>ID:</b> {{ character.id }}</span>
      <span><b>Name:</b> {{ character.name }}</span>
      <span><b>Gender:</b> {{ character.gender }}</span>
      <span><b>Species:</b> {{ character.species }}</span>
      <span><b>Last Episode:</b> {{ episodeInfo }}</span>
    </span>
    <span class="colFav">
      <button class="addButton" v-if="!favList.find((char) => char.id === character.id)" @click="addCharacter()">
        <img src="../assets/add-button.svg" />
      </button>
      <button class="addButton" v-if="favList.find((char) => char.id === character.id)" @click="removeCharacter()">
        <img src="../assets/fav-button.svg" />
      </button>
    </span>
  </div>
  <hr class="line" />
</template>

<script lang="ts">
import { CharacterType } from '@/types/Character';
import { onMounted, ref, defineComponent, PropType, SetupContext } from 'vue';

interface CharacterRowEvents {
  add: (character: CharacterType) => void;
  remove: (id: number) => void;
}

const episodeInfo = ref('')

export default defineComponent({
  name: 'CharacterRow',
  props: {
    character: {
      type: Object as PropType<CharacterType>,
      required: true,
    },
    favList: {
      type: Object as PropType<CharacterType[]>,
      required: true
    }
  },
  emits: ["add", "remove"],
  setup(props, { emit }: SetupContext<CharacterRowEvents>) {
    function getGenderIcon(gender: string) {
      switch (gender) {
        case 'Male':
          return require('@/assets/male.svg');
        case 'Female':
          return require('@/assets/female.svg');
        case 'Genderless':
          return require('@/assets/genderless.svg');
        default:
          return require('@/assets/unknown.svg');
      }
    }
    async function fetchData() {
      const lastEp = props.character.episode[props.character.episode.length - 1]
      try {
        const response = await fetch(lastEp);
        const data = await response.json();
        episodeInfo.value = data.episode
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    }

    onMounted(() => {
      fetchData();
    });

    function addCharacter() {
      emit("add", props.character);
    }

    function removeCharacter() {
      emit("remove", props.character.id);
    }

    return {
      episodeInfo,
      props,
      getGenderIcon,
      addCharacter,
      removeCharacter
    }
  }

})
</script>

<style scoped lang="scss">
@import "@/styles/variables.scss";

.characterRow {
  display: flex;
  align-items: center;
  padding: 8px 0;
  width: 100%;
  font-size: 16px;

  @include lg {
    font-size: 10px;
  }
}

.col {
  width: 15%;
  display: flex;

  img {
    margin-right: 4px;
  }

  @include lg {
    display: none;
  }
}

.colName {
  width: 20%;
  padding-right: 10px;
  display: block;

  @include lg {
    display: none;
  }
}

.colImg {
  position: relative;
  padding-left: 140px;
  width: 10%;

  img {
    width: 43px;
    height: 68px;
  }

  .characterImg {
    position: relative;
    display: flex;
    width: 43px;
    height: 68px;

    .ribbon {
      position: absolute;
      top: -5px;
      right: -16px;

      img {
        width: 32px;
        height: 32px;
      }
    }
  }

  @include lg {
    padding-left: 20px;
    width: 20%;
  }
}

.dead {
  filter: grayscale(100%);
}

.colFav {
  width: 15%;

  @include lg {
    width: 25%;
  }
}

.colInfo {
  display: none;

  flex: 1;

  @include lg {
    display: flex;
    flex-direction: column;
  }
}

.line {
  border: 1px solid $line-color;
  margin: 0;
}

.addButton {
  background-color: transparent;
  border: none;
  cursor: pointer;

  &:hover {
    opacity: 0.8;
  }
}
</style>
