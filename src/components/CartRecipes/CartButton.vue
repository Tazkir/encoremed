<script setup lang="ts">
import {
  Sheet,
  SheetContent,
  SheetHeader,
  SheetTitle,
  SheetTrigger,
  SheetClose,
} from '@/components/ui/sheet'
import { useSavedRecipesStore } from '@/stores/recipes'
import { Icon } from '@iconify/vue'
import CartList from './CartList.vue'
import { ref, watch } from 'vue'
import { Button } from '@/components/ui/button'

const store = useSavedRecipesStore()
const count = ref(store.savedRecipesCount())

watch(
  () => store.savedRecipesCount(),
  newCount => {
    count.value = newCount
  },
)
</script>

<template>
  <Sheet modal side="right" class="z-[9999]">
    <SheetTrigger as-child>
      <Button variant="outline" size="icon" class="relative">
        <span
          class="absolute -top-0 -right-2 bg-green-500 text-white rounded-full px-1 text-xs font-semibold"
          >{{ count }}</span
        >
        <Icon icon="ph:book-bookmark-thin" class="w-6 h-6" />
      </Button>
    </SheetTrigger>
    <SheetContent
      class="w-[400px] sm:w-[100%] flex flex-col justify-start items-center gap-5"
    >
      <SheetHeader>
        <SheetTitle>Your Saved Recipes</SheetTitle>
      </SheetHeader>
      <SheetClose>
        <CartList />
      </SheetClose>
    </SheetContent>
  </Sheet>
</template>
