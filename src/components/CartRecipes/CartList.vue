<script setup lang="ts">
import { slugify } from '@/lib/slugify'
import type { Recipe } from '@/lib/utils/Interfaces'
import { useSavedRecipesStore } from '@/stores/recipes'
import { useRouter } from 'vue-router'
import { ScrollArea } from '../ui/scroll-area'
import BookmarkButton from '../template/button/BookmarkButton.vue'

const router = useRouter()

const store = useSavedRecipesStore()

const recipes: Recipe[] = store.getSavedRecipes
</script>

<template>
  <ScrollArea
    class="w-full h-[50em] rounded-md max-sm:ml-3 max-sm:h-[35em] shadow-lg"
  >
    <div class="w-full grid grid-cols-2 gap-5">
      <div
        v-for="recipe in recipes"
        :key="recipe.name"
        class="w-full flex flex-col items-center justify-center text-center py-5 px-2 shadow-lg rounded-md border border-foreground/10 bg-foreground/5 cursor-pointer"
      >
        <div
          class="w-[90%] h-[5em] rounded-lg overflow-hidden"
          @click="router.push(`/recipe-detail/${slugify(recipe.name)}`)"
        >
          <img
            :src="recipe.image[0]"
            :alt="recipe.name"
            class="w-full h-full object-cover rounded-lg"
          />
        </div>

        <div class="w-full flex flex-col gap-3 justify-between items-center">
          <h1
            @click="router.push(`/recipe-detail/${slugify(recipe.name)}`)"
            class="text-lg font-semibold line-clamp-1"
          >
            {{ recipe.name }}
          </h1>

          <BookmarkButton :recipe="recipe" />
        </div>
      </div>
    </div>
  </ScrollArea>
</template>
