<script lang='ts' setup>
import { ref, onMounted, computed } from 'vue'
import type { Ref } from 'vue'
import ListItem from './ListItem.vue'
type Item = { id: number, title: string; checked?: boolean }
const initListItems = (): void => {
    // Obtener los items desde la API y actualizar storageItems
    fetch('http://localhost:3000/items')
        .then(response => response.json())
        .then((listItems: Item[]) => {
            storageItems.value = listItems
        })
        .catch(() => {
            storageItems.value = []
        })

        console.log(storageItems)
}

const updateItem = (item: Item): void => {
    const updatedItem = findItemInList(item)
    if (updatedItem) {
        toggleItemChecked(updatedItem)
        setToStorage(storageItems.value)
    }
}
const findItemInList = (item: Item): Item | undefined => {
    return storageItems.value.find(
        (itemInList: Item) => itemInList.title === item.title
    )
}
const toggleItemChecked = (item: Item): void => {
    item.checked = !item.checked
}

const sortedList = computed(() =>
    [...storageItems.value].sort((a, b) => (a.checked ? 1 : 0) -
        (b.checked ? 1 : 0))
)

const setToStorage = (items: Item[]): void => {
    localStorage.setItem('list-items', JSON.stringify(items))
}
const getFromStorage = (): Item[] | [] => {
    const stored = localStorage.getItem('list-items')
    if (stored) {
        return JSON.parse(stored)
    }
    return []
}

const storageItems: Ref<Item[]> = ref([])

onMounted(() => {
    initListItems()
    storageItems.value = getFromStorage()
})

</script>
<template>
    <ul>
        <li :key="item.id" v-for="item in sortedList" class="todo-list-item">
                <ListItem :is-checked="item.checked" @click="updateItem(item)">
                {{ item.title }}
                </ListItem>
                <!--<button @click="deleteItem(item)" class="todo-delete-btn">Eliminar</button>-->
            </li>
    </ul>
</template>
<style scoped>
ul {
    list-style: none;
}

li {

    margin: 0.4rem 0;
}
</style>