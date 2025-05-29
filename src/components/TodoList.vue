<script lang='ts' setup>
import { ref, onMounted, computed } from 'vue'
import type { Ref } from 'vue'
import ListItem from './ListItem.vue'
type Item = { id: number, title: string; checked?: boolean }

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

const saveTask = async (item: Item): Promise<void> => {
    try {
        await fetch('http://localhost:3000/items', {
            method: 'POST',
            body: JSON.stringify(item),
            headers: {
                'Content-Type': 'application/json'
            }
        })
    } catch (error) {
        console.error('Failed to add a item:', error)
    }
}

const getFromJsonServer = async (): Promise<Item[]> => {
    try {
        const response = await fetch('http://localhost:3000/items')
        return await response.json()
    } catch {
        return []
    }
}

const storageItems: Ref<Item[]> = ref([])

// Add the missing task variable for v-model binding
const taskName = ref('')

onMounted(async () => {
    storageItems.value = await getFromJsonServer()
})

async function addTask() {
    if (!taskName.value.trim()) return;
    // Obtener el último id
    const lastId = storageItems.value.length > 0
        ? Math.max(...storageItems.value.map(item => item.id))
        : 0;
    const newItem: Item = {
        id: lastId + 1,
        title: taskName.value,
        checked: false
    };
    await saveTask(newItem);
    storageItems.value = await getFromJsonServer();
    taskName.value = '';
}
</script>
<template>
    <input v-model="taskName" placeholder="Buy some milk" />
    <button @click="addTask">Add task</button>
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