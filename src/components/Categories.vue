<template>
  <div class="text-center mb-10 relative">
    <div class="absolute bottom-2 w-full h-1 bg-gray-200">
      <div class="bg-blue-500 h-full" :style="{ width: width + '%' }" />
    </div>
    <ul class="flex">
      <li
        v-for="category in categories"
        :key="category"
        class="category flex-1 pb-5 relative"
        :class="{ active: category.active }"
      >
        {{ category.name }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      width: 0,
      categories: [],
    }
  },

  computed: {
    steps() {
      return this.$store.state.steps.all
    },
    currentStepIndex() {
      return this.$store.state.steps.stepIndex
    },
  },

  watch: {
    currentStepIndex(newValue, oldValue) {
      if (newValue > oldValue) {
        this.progressBarIncrement(newValue)
      } else {
        this.progressBarDecrement(newValue + 1)
      }
    },
  },

  mounted() {
    this.distinctCategory()
    if (this.currentStepIndex > 0) {
      for (let i = 0; i < this.currentStepIndex + 1; i++) {
        this.progressBarIncrement(i)
      }
    } else {
      this.progressBarIncrement(this.currentStepIndex)
    }
  },

  methods: {
    distinctCategory() {
      this.steps.forEach((value) => {
        const category = this.categories.find((element) => element.name === value.category)
        if (typeof category === 'undefined') {
          const categoryIndex = this.steps.map((step) => step.category === value.category).lastIndexOf(true)
          this.categories.push({ name: this.steps[categoryIndex].category, active: false, index: categoryIndex })
        }
      })
    },
    progressBarIncrement(index) {
      this.width += 100 / this.categories.length / this.countStepByCategory(index)
      this.changeCategoryStatus(index, true)
    },
    progressBarDecrement(index) {
      this.width -= 100 / this.categories.length / this.countStepByCategory(index)
      this.changeCategoryStatus(index, false)
    },
    countStepByCategory(index) {
      return this.steps.reduce(
        (counter, obj) => (obj.category === this.steps[index].category ? (counter += 1) : counter),
        0,
      )
    },
    changeCategoryStatus(index, active) {
      const categoryIndex = this.categories.findIndex((element) => index === element.index)
      if (categoryIndex >= 0) {
        this.categories[categoryIndex].active = active
      }
    },
  },
}
</script>

<style scoped>
.category:after {
  content: '';
  @apply absolute bottom-0 -right-2.5 w-5 h-5 rounded-full bg-white border-2 border-gray-200;
}
.category:last-child:after {
  @apply right-0;
}
.category.active:after {
  @apply bg-blue-500;
}
</style>
