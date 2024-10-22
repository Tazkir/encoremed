<script setup lang="ts">
import { BookMinusIcon, BookPlusIcon } from 'lucide-vue-next'
import { useSavedRecipesStore } from '@/stores/recipes'
import type { Recipe } from '@/lib/utils/Interfaces'
import { Button } from '@/components/ui/button'
import { computed } from 'vue'

const props = defineProps<{
  recipe: Recipe | null
}>()

const recipesStore = useSavedRecipesStore()

const isBookmarked = computed(() => {
  return props.recipe ? recipesStore.isRecipeSaved(props.recipe) : false
})

const addRecipe = () => {
  if (props.recipe) {
    recipesStore.setSavedRecipes(props.recipe)
  }
}

const removeRecipe = () => {
  if (props.recipe) {
    recipesStore.removeSavedRecipe(props.recipe)
  }
}
</script>

<template>
  <div class="flex gap-2">
    <Button
      variant="default"
      size="icon"
      @click="addRecipe"
      :disabled="isBookmarked"
    >
      <BookPlusIcon class="w-5 h-5" />
    </Button>

    <Button
      variant="default"
      size="icon"
      @click="removeRecipe"
      :disabled="!isBookmarked"
    >
      <BookMinusIcon class="w-5 h-5" />
    </Button>
  </div>
</template>
