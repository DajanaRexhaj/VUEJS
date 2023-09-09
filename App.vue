<script setup>
import { ref, onMounted, computed, watch } from 'vue'

/* Start variables/ const/functions that are needed for a todo list: 
          an array that will store the input from user for a chore
          the name of each chore
          category of this input 
          content of this input
          list the chores in an ascendent order

*/

const todos = ref([])
const name = ref('')
const inputContent = ref('')
const inputCategory = ref(null)
const todosASC = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

/* Populate the todo list. Check if:
      the user has not typed anything or chosen a category, return.
      else push todo content category and sort it in an asc order. 
      In the end save your todos in your storage.*/

const addTodo = () => {
  if(inputContent.value.trim() === '' || inputCategory.value === null){
    return
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createdAt: new Date().getTime()
  })

  /* Set category and content to empty/null in the list after the user has typed one */
inputContent.value = ''
inputCategory.value = null
}



const removeTodo = todo => {
  todos.value = todos.value.filter( t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos',JSON.stringify(newVal))
}, { deep: true })
/* Save the name of user even when the page reloads */

watch(name, (newVal) =>{
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})


</script>

<template>

<main class = "app">
  <section class="greeting">
    <h2 class="title">
      Hello, <input type="text" placeholder="Name" v-model="name"/>
    </h2>
  </section>

  <section class="create-todo">
    <h3>Create your list</h3>
    <form @submit.prevent="addTodo">
    <h4>What do you want to add in your list?</h4>
    <input type="text" placeholder="Type something to remind you later" v-model="inputContent"/>

    <h4>Choose a category</h4>
    
    <div class="options">
      <label>
      <input type="radio" name="category" value="university" v-model="inputCategory"/>
      <span class="bubble university"></span>
      <div>University</div>
    </label>

    <label>
      <input type="radio" name="category" value="personal" v-model="inputCategory"/>
      <span class="bubble personal"></span>
      <div>Personal</div>
    </label>

    <label>
      <input type="radio" name="category" value="ideas" v-model="inputCategory"/>
      <span class="bubble ideas"></span>
      <div>Ideas</div>
    </label>

    <label>
      <input type="radio" name="category" value="movies" v-model="inputCategory"/>
      <span class="bubble movies"></span>
      <div>Movies to watch</div>
    </label>
  </div>

  <input type="submit" value="Add todo">
    </form>
  </section>

  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <div v-for = "todo in todosASC" :class = "`todo-item ${todo.done && 'done'}`">
        <label>
          <input type="checkbox" v-model="todo.done"/>
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

