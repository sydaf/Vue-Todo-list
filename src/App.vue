<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

// Ascending the todo list (by latest)
const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
}))

const addTodo = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
        return
    }

    // load the todo in an array
    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: new Date().getTime()
    })

    input.content.value  = ''
    input.category.value  = null
}

const removeTodo = todo => {
    todos.value = todos.value.filter(t => t !== todo)
}

// watch the changes of the todos input 
watch(todos, newVal => {
    localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

// watch the change of the name input
watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
})

// Grab Name & Todo item and save it to local storage
onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                What's up, <input type="text" placeholder="Name here" v-model="name" />
            </h2>
        </section>

        <section class="create-todo">
            <h2>CREATE TODO</h2>

            <form @submit.prevent="addTodo">
                <h4>What's on your todo</h4>
                <input type="text" placeholder="e.g. make a video" v-model="input_content" />

                <h4>Pick a category</h4>

                <div class="options">

                    <label>
                        <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
                        <div class="bubble">Business</div>
                    </label>

                    <label>
                        <input type="radio" name="category" id="category1" value="personal" v-model="input_category" />
                        <div class="bubble">Personal</div>
                    </label>

                </div>

                <input class="add-todo" type="submit" value="Add todo">
            </form>
        </section>

        <section class="todo-list">
            <h3>TODO List</h3>
            <div class="list">
                <div v-for="todo in todos_asc" :class="`todo-item`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content">
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">Delete</button>
                    </div>
                </div>
            </div>
        </section>
    </main>
</template>