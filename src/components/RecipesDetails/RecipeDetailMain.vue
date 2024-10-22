<script setup lang="ts">
import { unslugify, normalizeText } from '@/lib/slugify'
import type { Recipe } from '@/lib/utils/Interfaces'
import { onMounted, ref, watch } from 'vue'
import { useRoute } from 'vue-router'
import BookmarkButton from '../template/button/BookmarkButton.vue'
import { Loader2Icon } from 'lucide-vue-next'

const route = useRoute()

const unslug = ref(route.params.slug as string)
const slug = ref(unslugify(unslug.value))

console.log(slug.value)

const recipes = ref<Recipe[]>([])
const isLoading = ref(true)
const error = ref<string | null>(null)

const fetchRecipes = async () => {
  isLoading.value = true
  error.value = null
  try {
    const response = await fetch(
      'https://raw.githubusercontent.com/micahcochran/json-cookbook/refs/heads/main/cookbook-100.json',
    )
    if (!response.ok) {
      throw new Error('Network response was not ok')
    }
    const allRecipes: Recipe[] = await response.json()
    recipes.value = allRecipes.filter(recipe => {
      const normalizedRecipeName = normalizeText(recipe.name)
      const normalizedSlug = normalizeText(slug.value)

      const slugWords = normalizedSlug.split(/\s+/)

      return slugWords.every(word => normalizedRecipeName.includes(word))
    })
  } catch (err) {
    console.error('Error fetching recipes:', err)
    error.value = 'Failed to fetch recipes. Please try again later.'
  } finally {
    isLoading.value = false
  }
}

onMounted(fetchRecipes)

watch(
  () => route.params.slug,
  newSlug => {
    unslug.value = newSlug as string
    slug.value = unslugify(unslug.value)
    fetchRecipes()
  },
)
</script>

<template>
  <div
    v-if="isLoading"
    class="w-full flex flex-col gap-2 justify-center items-center"
  >
    <Loader2Icon class="w-10 h-10 animate-spin" />
    <p class="text-xl font-medium">Loading...</p>
  </div>
  <div v-else-if="error" class="w-full flex justify-center items-center">
    {{ error }}
  </div>

  <div v-else class="w-full flex flex-col gap-5">
    <div
      v-for="recipe in recipes"
      :key="recipe.name"
      class="w-full flex md:flex-row flex-col justify-center items-start gap-5"
    >
      <div class="w-full flex flex-1 justify-center items-center">
        <div class="w-[90%] h-[100%] rounded-lg overflow-hidden cursor-pointer">
          <img
            :src="recipe.image[0]"
            alt="recipe image"
            class="w-full h-full object-cover transition-all duration-300 hover:scale-110"
          />
        </div>
      </div>

      <div class="w-full flex flex-col flex-1 justify-center items-start gap-5">
        <div class="w-full flex flex-col gap-2">
          <h1 class="text-5xl font-bold">{{ recipe.name }}</h1>
          <h2 class="text-sm font-medium">Author: {{ recipe.author.name }}</h2>
        </div>
        <BookmarkButton :recipe="recipe" />
        <p class="text-lg">{{ recipe.description }}</p>

        <div class="w-full flex flex-col gap-5" v-if="recipes.length > 0">
          <h2 class="text-2xl font-bold">Ingredients</h2>
          <ul class="list-disc list-inside">
            <li
              v-for="(ingredient, index) in recipes[0].recipeIngredient"
              :key="index"
            >
              {{ ingredient }}
            </li>
          </ul>
        </div>
        <div class="w-full flex flex-col gap-5" v-if="recipes.length > 0">
          <h2 class="text-2xl font-bold">Instructions</h2>
          <ul class="list-inside list-decimal">
            <li
              v-for="(instruction, index) in recipes[0].recipeInstructions"
              :key="index"
            >
              {{ instruction.text }}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
