<script setup>
definePageMeta({ layout: 'default' })
import { useRouter } from 'vue-router'
import { ref, computed, onMounted } from 'vue'
import { useRecipeStore } from '~/store/recipes'
import Headers from '@/components/Headers.vue'
import Explorebutton from '@/components/Explorebutton.vue'
import Nav from '~/components/Nav.vue'

const recipeStore = useRecipeStore()
const router = useRouter()
const search = ref('')
const showSearchModal = ref(false)
const showUserOptions = ref(false)

onMounted(async () => {
  await recipeStore.fetchAllRecipes()
})

const allRecipes = computed(() => recipeStore.allRecipes)

const filteredRecipes = computed(() => {
  if (!search.value) return allRecipes.value
  return allRecipes.value.filter((recipe) =>
    recipe.user.username.toLowerCase().includes(search.value.toLowerCase()) ||
    recipe.categories.names.join(' ').toLowerCase().includes(search.value.toLowerCase())
  )
})

function closeSearchModal() {
  showSearchModal.value = false
}
function toggleUserContent() {
  showUserOptions.value = !showUserOptions.value
}
</script>

<template>
  <div class="app-shell">
    <Headers @open-search="showSearchModal = true" @open-user="toggleUserContent" />
    <Nav />

    <v-container fluid class="py-0">
      <v-menu v-model="showUserOptions" location="bottom end" :close-on-content-click="true">
        <template #activator="{ props }">
          <div class="user-container">
            <div v-bind="props" />
          </div>
        </template>
        <v-list density="comfortable">
          <v-list-item min-height="36" title="Saved recipes" />
          <v-list-item min-height="36" title="Log out" />
        </v-list>
      </v-menu>

      <v-sheet class="hero bg-leaf-vein" rounded="0" elevation="0">
        <div class="hero-inner">
          <Explorebutton />
        </div>
      </v-sheet>

      <v-container class="py-6">
        <v-row dense>
          <v-col cols="12" sm="6" md="4" v-for="n in 3" :key="n">
            <v-card rounded="lg" elevation="1" min-height="140" />
          </v-col>
        </v-row>
      </v-container>
    </v-container>

    <v-dialog v-model="showSearchModal" width="420">
      <v-card rounded="lg">
        <v-card-text>
          <v-text-field
            v-model="search"
            variant="outlined"
            density="comfortable"
            clearable
            hide-details
            placeholder="Search"
            @keydown.enter="closeSearchModal"
          />
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn color="primary" variant="flat" @click="closeSearchModal">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<style scoped>
.app-shell {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.bg-leaf-vein {
  --leaf-vein-size: 160px;
  --leaf-vein-bg: #fffdf7;
  background-color: var(--leaf-vein-bg);
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 200 200' fill='none'><g stroke='%237A8C5C' stroke-opacity='0.18' stroke-width='1.1' stroke-linecap='round'><path d='M20 60 C 60 30, 140 30, 180 60'/><path d='M20 100 C 60 70, 140 70, 180 100'/><path d='M20 140 C 60 110, 140 110, 180 140'/><path d='M40 20 C 70 60, 70 140, 40 180'/><path d='M100 20 C 130 60, 130 140, 100 180'/><path d='M160 20 C 190 60, 190 140, 160 180'/><g stroke-opacity='0.12'><path d='M10 30 C 60 50, 140 50, 190 30'/><path d='M10 170 C 60 150, 140 150, 190 170'/><path d='M30 10 C 50 60, 50 140, 30 190'/><path d='M170 10 C 150 60, 150 140, 170 190'/></g></g></svg>");
  background-repeat: repeat;
  background-size: var(--leaf-vein-size);
}
.hero {
  position: relative;
  display: grid;
  place-items: center;
  height: 72vh;
  overflow: hidden;
}
.hero-inner {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
}
.user-container { position: relative; display: inline-block; }
</style>

