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
  [...storageItems.value].sort((a, b) => (a.checked ? 1 : 0) - (b.checked ? 1 : 0))
)

const storageItems: Ref<Item[]> = ref([])

onMounted(() => {
    initListItems()
})

</script>
<template>
    <ul>
        <li :key='key' v-for='(item, key) in sortedList'>
            <ListItem :is-checked='item.checked' v-on:click.prevent="updateItem(item)">{{ item.title }}</ListItem>
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