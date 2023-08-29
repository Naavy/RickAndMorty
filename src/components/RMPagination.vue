<template>
  <div v-if="pages > 1" class="pagination">
    <button :disabled="currentPage === 1" class="tile" @click="goToPrevPage()">
      <img v-if="currentPage === 1" src="../assets/pages-arrow.svg" />
      <img v-if="currentPage > 1" class="arrow" src="../assets/color-arrow.svg" />
    </button>
    <button :class="['tile', currentPage === 1 ? 'active' : '']" @click="fetchPagedData(1)">1</button>
    <button v-for="pageNumber in pageNumbers" :key="pageNumber"
      :class="['tile', currentPage === pageNumber ? 'active' : '']" @click="fetchPagedData(pageNumber)">
      {{ pageNumber }}
    </button>
    <button :class="['tile', currentPage === pages ? 'active' : '']" @click="fetchPagedData(pages)">{{ pages }}</button>
    <button :disabled="currentPage === pages" class="tile" @click="goToNextPage()">
      <img v-if="currentPage === pages" class="arrow" src="../assets/pages-arrow.svg" />
      <img v-if="currentPage < pages" src="../assets/color-arrow.svg" />
    </button>
  </div>
</template>

<script lang="ts">
import { SetupContext, computed, ref, defineComponent } from 'vue';

export default defineComponent({
  name: 'RMPagination',
  props: {
    pages: {
      type: Number,
      required: true,
    }
  },
  setup(props, { emit }: SetupContext) {
    const currentPage = ref(1);
    const pageNumbers = computed(() => {
      if (props.pages <= 5) {
        return Array.from({ length: props.pages - 2 }, (_, index) => index + 2);
      } else if (currentPage.value <= 3) {
        return [2, 3, 4, '...']
      } else if (currentPage.value >= props.pages - 3) {
        return ['...', props.pages - 3, props.pages - 2, props.pages - 1]
      } else
        return ['...', currentPage.value - 1, currentPage.value, currentPage.value + 1, '...']
    })

    function goToNextPage() {
      currentPage.value = currentPage.value + 1
      emit("fetchData", currentPage.value)
    }

    function goToPrevPage() {
      currentPage.value = currentPage.value - 1
      emit("fetchData", currentPage.value)
    }

    function fetchPagedData(pageNumber: number | string) {
      if (typeof pageNumber === 'string') return ''
      currentPage.value = pageNumber
      emit("fetchData", currentPage.value)
    }

    return {
      currentPage,
      pageNumbers,
      goToNextPage,
      goToPrevPage,
      fetchPagedData
    }
  }
})
</script>

<style scoped lang="scss">
@import "@/styles/variables.scss";

.pagination {
  display: flex;
  margin: 40px 0 40px 140px;
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;

  * {
    display: flex;
  }

  @include lg {
    margin: 20px 0 20px 20px;
    justify-content: center;
  }
}

.tile {
  display: flex;
  min-width: 40px;
  height: 40px;
  padding: 12px;
  justify-content: center;
  align-items: center;
  border: 1px solid $primary-color;
  border-radius: 4px;
  box-sizing: border-box;
  margin: 10px 8px 10px 0;
  background-color: transparent;
  color: $primary-color;
  cursor: pointer;

  @include lg {
    min-width: 20px;
    height: 20px;
    padding: 5px;
  }
}

.active {
  background: $secondary-color;
  color: #fff;
  border: 1px solid $secondary-color;
}

.arrow {
  transform: rotate(180deg);
}
</style>
