<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import type { Ref } from 'vue'
import ListItem from './ListItem.vue'

// props
interface Item {
  title: string
  checked?: boolean
}

// data
const storageItems: Ref<Item[]> = ref([])

// methods
const setToStorage = (items: Item[]): void => {
  localStorage.setItem('list-items', JSON.stringify(items))
}

const getFromStorage = (): Item[] | [] => {
  const stored = localStorage.getItem('list-items')
  return stored ? JSON.parse(stored) : []
}

const initListItems = (): void => {
  if (storageItems.value?.length === 0) {
    const listItems: Item[] = [
      { title: 'Make a todo list app', checked: true },
      { title: 'Predict the weather', checked: false },
      { title: 'Play some tunes', checked: false },
      { title: "Les's get cooking", checked: false },
      { title: 'Pump some iron', checked: false },
      { title: 'Track my expenses', checked: false },
      { title: 'Organize a game night', checked: false },
      { title: 'Learn a new language', checked: false },
      { title: 'Publish my work' },
    ]

    setToStorage(listItems)
    storageItems.value = listItems
  }
}

/**
 * Perbarui status sebuah item dalam daftar.
 *
 * Mencari item yang sesuai dalam ListItems berdasarkan properti title lalu
 * membalikkan nilai properti checked pada item yang ditemukan.
 *
 * Catatan:
 * - Perbandingan dilakukan berdasarkan title, bukan referensi objek.
 * - Jika item tidak ditemukan, fungsi ini tidak melakukan apa-apa.
 *
 * @param {Item} item - Item sumber yang akan dicari dan diperbarui (digunakan title sebagai kunci pencarian).
 * @returns {void}
 */
const updateItem = (item: Item): void => {
  const updatedItem = findItemInList(item)
  if (updatedItem) {
    toggleItemChecked(updatedItem)
    setToStorage(storageItems.value)
  }
}

/**
 * Cari sebuah item dalam daftar ListItems berdasarkan title.
 *
 * Mengembalikan referensi ke objek Item yang pertama kali cocok dengan title
 * yang diberikan dari ListItems.value, atau undefined jika tidak ditemukan.
 *
 * Catatan:
 * - Pencarian sensitif terhadap nilai title persis (perbandingan ===).
 * - Mengembalikan referensi langsung ke objek dalam daftar, bukan salinan.
 *
 * @param {Item} item - Item yang title-nya digunakan sebagai kriteria pencarian.
 * @returns {Item | undefined} Item yang ditemukan atau undefined jika tidak ada.
 */
const findItemInList = (item: Item): Item | undefined => {
  return storageItems.value.find((ItemInList: Item) => ItemInList.title === item.title)
}

/**
 * Membalikkan nilai properti checked pada sebuah Item.
 *
 * Fungsi ini melakukan mutasi langsung pada objek item yang diberikan.
 * Jika item.checked bernilai true, menjadi false; jika false, menjadi true.
 *
 * Catatan:
 * - Tidak mengembalikan nilai; perubahan dilakukan in-place pada objek.
 *
 * @param {Item} item - Objek Item yang properti checked-nya akan dibalik.
 * @returns {void}
 */
const toggleItemChecked = (item: Item): void => {
  item.checked = !item.checked
}

const sortedList = computed(() =>
  [...storageItems.value].sort((a, b) => (a.checked ? 1 : 0) - (b.checked ? 1 : 0)),
)

// Life Cycle Hooks
onMounted(() => {
  const stored = getFromStorage()
  if (stored && stored.length > 0) {
    storageItems.value = stored
  } else {
    initListItems()
  }
})
</script>
<template>
  <div class="todo-container">
    <ul class="todo-list">
      <li v-for="(item, index) in sortedList" :key="item.title + index">
        <ListItem :isChecked="item.checked" @update="updateItem(item)">
          {{ item.title }}
        </ListItem>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.todo-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.todo-list {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}
</style>

<style scoped>
.todo-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.todo-list {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}
</style>
<style scoped>
.todo-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.todo-list {
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}
</style>
