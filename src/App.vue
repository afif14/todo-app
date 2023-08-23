<script setup>
import { ref, computed, watch, onMounted } from "vue"

// @ts-ignore
const todos = ref([])
const name = ref("")

const input_content = ref("")
const input_category = ref(null)

const todos_asc = computed(() =>
   todos.value.sort((a, b) => {
      // @ts-ignore
      return b.createdAt - a.createdAt
   })
)

const addTodo = () => {
   if (input_content.value.trim() === '' || input_category.value === null) {
      return
   }

   // @ts-ignore
   todos.value.push({
      content: input_content.value,
      category: input_category.value,
      createdAt: new Date().getTime(),
      done: false
   })
}

const removeTodo = (todo) => {
   todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
   localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
   localStorage.setItem("name", newVal)
})

onMounted(() => {
   name.value = localStorage.getItem("name") || ""
   todos.value = JSON.parse(localStorage.getItem('todos') || '') || []
})
// @ts-ignore
</script>

<template>
   <main class="app">
      <section class="greeting">
         <h2 class="title">What's up <input type="text" v-model="name" placeholder="Name's here" /></h2>
      </section>
      <section class="create-todo">
         <h3>Create a todo</h3>

         <form @submit.prevent="addTodo">
            <h4>What's on your todo list?</h4>
            <input type="text" v-model="input_content" placeholder="e.g. make a video" />
            <h4>Pick a category</h4>
            <div class="options">
               <label>
                  <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
                  <span class="bubble business"></span>
                  <div>Business</div>
               </label>
               <label>
                  <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
                  <span class="bubble personal"></span>
                  <div>Personal</div>
               </label>
            </div>
            <input type="submit" value="Add Todo">
         </form>
      </section>
      <section class="todo-list">
         <h3>Todo List</h3>
         <div class="list">
            <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
               <label>
                  <input type="checkbox" v-model="todo.done" />
                  <span :class="`bubble ${todo.category} `"></span>
               </label>
               <div class="todo-content">
                  <input type="text" v-model="todo.content" />
               </div>
               <div class="actions">
                  <button class="delete" @click="removeTodo(todo)"> Delete</button>
               </div>
            </div>
         </div>
      </section>
   </main>
</template>
