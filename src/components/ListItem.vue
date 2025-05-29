<script lang="ts" setup>
defineProps<{
    isChecked?: boolean | false
}>()
</script>
<template>
    <label class="list-item" :class="{ 'checked': isChecked }">
        <input type="checkbox" :checked="isChecked" class="checkbox" />
        <span class="checkmark"></span>
        <span class="text">
            <slot></slot>
        </span>
    </label>
</template>
<style scoped>
.list-item {
    display: flex;
    align-items: center;
    cursor: pointer;
    padding: 1rem;
    background: white;
    border-radius: 12px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
    transition: all 0.2s ease;
    border: 1px solid #f0f0f0;
    position: relative;
    overflow: hidden;
}

.list-item:hover {
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
    transform: translateY(-1px);
}

.checkbox {
    position: absolute;
    opacity: 0;
    cursor: pointer;
    z-index: 2;
}

.checkmark {
    height: 20px;
    width: 20px;
    background-color: #f8f9fa;
    border: 2px solid #e9ecef;
    border-radius: 50%;
    margin-right: 1rem;
    position: relative;
    transition: all 0.2s ease;
    flex-shrink: 0;
}

.checkmark::after {
    content: "";
    position: absolute;
    display: none;
    left: 6px;
    top: 2px;
    width: 6px;
    height: 12px;
    border: solid white;
    border-width: 0 2px 2px 0;
    transform: rotate(45deg);
}

.checkbox:checked ~ .checkmark {
    background-color: #667eea;
    border-color: #667eea;
}

.checkbox:checked ~ .checkmark::after {
    display: block;
}

.text {
    flex: 1;
    font-size: 1rem;
    color: #2d3748;
    transition: all 0.2s ease;
    line-height: 1.5;
}

.checked .text {
    text-decoration: line-through;
    color: #a0aec0;
}

.checked .list-item {
    background-color: #f7fafc;
}

@media (max-width: 768px) {
    .list-item {
        padding: 0.875rem;
    }
    
    .text {
        font-size: 0.95rem;
    }
}
</style>