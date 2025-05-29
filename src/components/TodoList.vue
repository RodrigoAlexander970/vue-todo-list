<script lang='ts' setup>
import { ref, onMounted, computed } from 'vue'
import type { Ref } from 'vue'
import ListItem from './ListItem.vue'
type Item = { id: number, title: string; checked?: boolean }

const updateItem = async (item: Item): Promise<void> => {
    const updatedItem = findItemInList(item)
    if (updatedItem) {
        toggleItemChecked(updatedItem)
        try {
            await fetch(`http://localhost:3000/items/${updatedItem.id}`, {
                method: 'PUT',
                body: JSON.stringify(updatedItem),
                headers: {
                    'Content-Type': 'application/json'
                }
            })
            storageItems.value = await getFromJsonServer()
        } catch (error) {
            console.error('Failed to update item:', error)
        }
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
        const itemWithStringId = { ...item, id: String(item.id) };
        const newItem = JSON.stringify(itemWithStringId);

        await fetch('http://localhost:3000/items', {
            method: 'POST',
            body: newItem,
            headers: {
                'Content-Type': 'application/json'
            }
        })
    } catch (error) {
        console.error('Failed to add a item:', error)
    }
}

const deleteTask = async (item: Item): Promise<void> => {
    try {
        await fetch(`http://localhost:3000/items/${item.id}`, {
            method: 'DELETE'
        });
        storageItems.value = await getFromJsonServer();
    } catch (error) {
        console.error('Failed to delete an item:', error)
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
    <main class="container">
        <div class="add-task-section">
            <div class="input-group">
                <input v-model="taskName" placeholder="Añadir nueva tarea..." class="task-input"
                    @keyup.enter="addTask" />
                <button @click="addTask" class="add-button">
                    <span class="plus-icon">+</span>
                    Añadir
                </button>
            </div>
        </div>

        <div class="tasks-section">
            <div v-if="storageItems.length === 0" class="empty-state">
                <div class="empty-icon">📝</div>
                <h3>No hay tareas aún</h3>
                <p>Añade tu primera tarea para comenzar</p>
            </div>

            <ul v-else class="tasks-list">
                <li :key="item.id" v-for="item in sortedList" class="task-item">
                    <ListItem :is-checked="item.checked" @click="updateItem(item)">
                        {{ item.title }}
                    </ListItem>
                    <button @click="deleteTask(item)" class="delete-button">
                        <span class="delete-icon">🗑️</span>
                    </button>
                </li>
            </ul>
        </div>
    </main>
</template>
<style scoped>
.container {
    width: 100%;
    max-width: none;
    margin: 0;
    padding: 2rem 1.5rem;
    min-height: calc(100vh - 200px);
}

.add-task-section {
    margin-bottom: 2rem;
}

.input-group {
    display: flex;
    gap: 0.75rem;
    align-items: center;
}

.task-input {
    flex: 1;
    padding: 1rem 1.25rem;
    border: 2px solid #e2e8f0;
    border-radius: 12px;
    font-size: 1rem;
    background: white;
    transition: all 0.2s ease;
    outline: none;
}

.task-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.task-input::placeholder {
    color: #a0aec0;
}

.add-button {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 1rem 1.5rem;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 12px;
    font-size: 1rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
    white-space: nowrap;
}

.add-button:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 16px rgba(102, 126, 234, 0.3);
}

.plus-icon {
    font-size: 1.2rem;
    font-weight: bold;
}

.tasks-section {
    background: #f8f9fa;
    border-radius: 16px;
    padding: 1.5rem;
    min-height: 300px;
}

.empty-state {
    text-align: center;
    padding: 3rem 1rem;
    color: #718096;
}

.empty-icon {
    font-size: 3rem;
    margin-bottom: 1rem;
}

.empty-state h3 {
    margin: 0 0 0.5rem 0;
    font-size: 1.25rem;
    color: #4a5568;
}

.empty-state p {
    margin: 0;
    font-size: 1rem;
}

.tasks-list {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
}

.task-item {
    display: flex;
    align-items: center;
    gap: 0.75rem;
}

.task-item :deep(.list-item) {
    flex: 1;
}

.delete-button {
    padding: 0.75rem;
    background: #fed7d7;
    color: #e53e3e;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    min-width: 44px;
    height: 44px;
}

.delete-button:hover {
    background: #feb2b2;
    transform: scale(1.05);
}

.delete-icon {
    font-size: 1rem;
}

@media (max-width: 768px) {
    .container {
        padding: 1.5rem 1rem;
    }

    .input-group {
        flex-direction: column;
        gap: 1rem;
    }

    .task-input,
    .add-button {
        width: 100%;
    }

    .add-button {
        justify-content: center;
    }

    .tasks-section {
        padding: 1rem;
    }

    .task-item {
        gap: 0.5rem;
    }

    .delete-button {
        min-width: 40px;
        height: 40px;
        padding: 0.5rem;
    }
}
</style>