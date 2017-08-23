<template>
  <div class="categories-select">
    <span class="tag"
          v-for="category in categories"
          :key="category._id"
          :class="{
            'active': isActive(category._id),
            'disabled': isDisabled(category._id)
          }"
          @click.prevent="toggleCategory(category._id)">
      {{ category.title }}
    </span>
    <p class="small-info">
      {{ selectedCount }} von {{ selectedMax }} Kategorien ausgewählt
    </p>
  </div>
</template>

<script>
  import {mapGetters} from 'vuex'

  export default {
    name: 'hc-categories-select',
    props: {
      value: {
        type: Array,
        default: []
      }
    },
    data () {
      return {
        selectedMax: 3,
        selectedCategoryIds: []
      }
    },
    computed: {
      ...mapGetters({
        categories: 'categories/all'
      }),
      selectedCount () {
        return this.selectedCategoryIds.length
      },
      reachedMaximum () {
        return this.selectedCount >= this.selectedMax
      }
    },
    watch: {
      selectedCategoryIds (categoryIds) {
        this.$emit('input', categoryIds)
      }
    },
    methods: {
      toggleCategory (id) {
        const index = this.selectedCategoryIds.indexOf(id)
        if (index > -1) {
          this.selectedCategoryIds.splice(index, 1)
        } else if (!this.isDisabled(id)) {
          this.selectedCategoryIds.push(id)
        }
      },
      isActive (id) {
        if (this.selectedCategoryIds.indexOf(id) > -1) {
          return true
        }
        return false
      },
      isDisabled (id) {
        if (this.reachedMaximum && !this.isActive(id)) {
          return true
        }
        return false
      }
    },
    created () {
      this.selectedCategoryIds = this.value
    }
  }
</script>

<style lang="scss" scoped>
  @import "~assets/styles/utilities";

  .tag {
    cursor: pointer;
    transition: all 0.1s ease-out;
    user-select: none;
    margin-bottom: 5px;
    margin-right: 5px;

    &:hover {
      background-color: $grey-lighter;
    }

    &.active {
      background-color: $primary;
      color: $white;
    }

    &.disabled {
      background-color: $white-ter;
      opacity: 0.5;
      cursor: default;
    }
  }
</style>