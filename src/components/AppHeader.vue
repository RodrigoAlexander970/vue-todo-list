<template>
    <header class="header">
        <div class="header-content">
            <h1 class="title"> 
                Todo List
            </h1>
        </div>
    </header>
</template>

<style scoped>
.header {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    padding: 2rem 0;
    box-shadow: 0 2px 20px rgba(0, 0, 0, 0.1);
}

.header-content {
    max-width: 800px;
    margin: 0 auto;
    padding: 0 1.5rem;
    text-align: center;
}

.title {
    font-size: 2.5rem;
    font-weight: 300;
    margin: 0 0 0.5rem 0;
    letter-spacing: -0.02em;
}

.icon {
    font-size: 2rem;
    margin-right: 0.5rem;
    vertical-align: middle;
}

.subtitle {
    font-size: 1.1rem;
    margin: 0;
    opacity: 0.9;
    font-weight: 300;
}

@media (max-width: 768px) {
    .title {
        font-size: 2rem;
    }
    
    .icon {
        font-size: 1.5rem;
    }
    
    .subtitle {
        font-size: 1rem;
    }
}
</style>