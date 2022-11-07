<template>
  <main class="app">
    <div class="container">
      <section class="greeting">
        <div class="greeting__body">
          <h2 class="greeting__message">Hello,</h2>
          <input class="greeting__input" type="text" placeholder="name" v-model="userName">
        </div>
      </section>
      <section class="actions">
        <form @submit.prevent="AddTodo" class="action__form">
          <h3 class="action__title">Create a to-do</h3>
          <input class="actions__input"
            type="text" 
            placeholder="e.g. make a pizza" 
            v-model="inputString" />

          <div class="actions__categories">

            <label class="actions__label">
              <input
                v-model="category" 
                class="actions__radio" 
                type="radio" 
                name="category" 
                value="personal" />
                <span class="circle personal"></span>
                <div>personal</div>
            </label>

            <label class="actions__label">
              <input 
                v-model="category" 
                class="actions__radio" 
                type="radio" 
                name="category" 
                value="work" />
                <span class="circle work"></span>
                <div>work</div>
            </label>

            <label class="actions__label">
              <input 
                v-model="category" 
                class="actions__radio" 
                type="radio" 
                name="category" 
                value="other" />
                <span class="circle other"></span>
                <div>other</div>
            </label>

          </div>
        <div class="actions__error" v-if="categoryError">You need to pick up the category</div>
        <button class="actions__btn" type="submit">Add to-do</button>
      </form>
      </section>
      <section class="todos">
        <h4 class="todos__title">Todo list</h4>
        <div class="todos__list">
          <transition-group name="list">
          <div v-for="todo in sortedTodos" :key="todo.id" :class="`todos__item ${todo.done && 'done'}`">
            <div class="todos__body">
              <label>
                <input type="checkbox" v-model="todo.done">
                <span :class="`circle ${todo.category}`"></span>
              </label>
              <div class="todos__content">
                <input type="text" v-model="todo.content">
              </div>
              <div class="todos__actions">
                <button class="todos__delete" @click="removeTodo(todo)">
                  <svg width="25" height="25" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M114 100L163 51C164.826 49.1371 165.842 46.629 165.829 44.0207C165.816 41.4124 164.774 38.9147 162.93 37.0704C161.085 35.226 158.588 34.184 155.979 34.1708C153.371 34.1577 150.863 35.1744 149 37L100 86L51 37C49.1371 35.1744 46.629 34.1577 44.0207 34.1708C41.4124 34.184 38.9147 35.226 37.0704 37.0704C35.226 38.9147 34.184 41.4124 34.1708 44.0207C34.1577 46.629 35.1744 49.1371 37 51L86 100L37 149C35.1744 150.863 34.1577 153.371 34.1708 155.979C34.184 158.588 35.226 161.085 37.0704 162.93C38.9147 164.774 41.4124 165.816 44.0207 165.829C46.629 165.842 49.1371 164.826 51 163L100 114L149 163C150.863 164.826 153.371 165.842 155.979 165.829C158.588 165.816 161.085 164.774 162.93 162.93C164.774 161.085 165.816 158.588 165.829 155.979C165.842 153.371 164.826 150.863 163 149L114 100Z" fill="black"/>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </transition-group>
        </div>
        <div class="todos__empty" v-if="sortedTodos.length === 0">You got nothing to do</div>
      </section>
    </div>
  </main>
</template>

<script>
import { ref, onMounted, watch, computed } from 'vue'
export default {
  setup() {
    const userName = ref('');
    const todos = ref([])
    const inputString = ref('')
    const category = ref(null)
    const categoryError = ref(false)

    const sortedTodos = computed(() => todos.value.sort((todo1, todo2) => {
      return todo2.createdAt - todo1.createdAt;
    }))
    

    const AddTodo = () => {
      if(inputString.value.trim() === '') {
        return
      } else if (category.value === null) {
        categoryError.value = true
        return
      }
      todos.value.push({
        content: inputString.value,
        category: category.value,
        createdAt: new Date().getTime(),
        done: false
      })
      inputString.value = ''
      category.value = null
      categoryError.value = false
    }

    const removeTodo = (todo) => {
      todos.value = todos.value.filter(item => item != todo)
    }
    watch(todos, newValue => {
      localStorage.setItem('todos', JSON.stringify(newValue))
    }, {deep: true})
    watch(userName, (newValue) => {
      localStorage.setItem('userName', newValue)
    });
    onMounted(() => {
      userName.value = localStorage.getItem('userName') || '';
      todos.value = JSON.parse(localStorage.getItem('todos')) || [];
    })
    return {
      userName,
      todos, 
      inputString,
      category,
      sortedTodos,
      AddTodo,
      removeTodo,
      categoryError,
    }
  }

}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  background: #0b1120;
  color: #fff;
  font-family: 'Rubik', sans-serif;
}
input, button {
  appearance: none;
  border: none;
  outline: none;
  background: none;
  cursor: initial;
}
.app {
  display: flex;
  justify-content:center;
  align-items: center;
  margin-top: 50px;

}
.container {
  max-width: 700px;
  margin: 0 auto;
  padding: 0 15px;
}
.greeting {
		// .greeting__body
		&__body {
      display: flex;
      justify-content:center;
      align-items: center;
      gap: 5px;
      margin-bottom: 15px;
      
    }
    // .greeting___message
    &__message {
      font-size: 25px;
      font-weight: 700;
      color: #909fb3;
    }
		// .greeting__input
		&__input {
      width: 100%;
      font-size: 25px;
      font-weight: 700;
      color: #0ea5e9;
      &::placeholder {
        color: #6a717b;
      }
    }
}
.actions {
		// .actions__title
		&__title {
      margin-bottom: 30px;
    }
		// .actions__input
		&__input {
      width: 100%;
      padding: 15px;
      font-size: 18px;
      color: #909fb3;
      margin-top: 15px;
      background-color: #1e293b;

      @media (any-hover: hover){
        &:hover{
          background-color: #334663;
        }
      }
    }
		// .actions__categories
		&__categories {
      margin-top: 15px;
      display: flex;
      justify-content:center;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
    }
		// .actions__label
		&__label {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 10px;
      cursor: pointer;
    }
		// .actions__radio
		&__radio {}
		// .actions__btn
		&__btn {
      cursor: pointer;
      margin-top: 15px;
      font-size: 16px;
      font-weight: 500;
      width: 100%;
      padding: 15px;
      background: #235de6; 
      border-radius: 10px;
      transition: background 0.3s ease 0s;
        &:hover{
          background: #094ef0;
        }
    }
}
input[type="radio"],
input[type="checkbox"] {
  display: none;
}

.circle {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid teal;
  margin-bottom: 10px;
  &::after{
    position: absolute;
    content:'';
    display: block;
    opacity: 0;
    width: 0px;
    height: 0px;
    border-radius: 50%;
    transition: all 0.3s ease 0s;
  }
}
input:checked ~ .circle::after {
    width: 10px;
    height: 10px;
    opacity: 1;
}
.circle.personal::after {
  background: #0284c7;
}
.circle.work::after {
  background: #be185d; 
}
.circle.other::after {
  background: #008080; 
}
.personal {
  border: 2px solid #0284c7;
}
.work {
  border: 2px solid #be185d;
}
.other {
  border: 2px solid #008080;
}

.todos {
  margin-top: 30px;
		// .todos__title
		&__title {
    }
		// .todos__list
		&__list {
      margin-top: 25px;
      display: flex;
      flex-direction: column;
      gap: 15px;
    }
		// .todos__item
		&__item {
      &.done {
        .todos__content {
          input {
            text-decoration: line-through;
            color: #6a717b;
          }
        }
      }
    }
		// .todos__body
		&__body {
      display: flex;
      justify-content:center;
      align-items: center;
      background-color: #1e293b;
      padding: 15px;
      gap: 10px;
      label {
        cursor: pointer;
        margin-top: 10px;
        background: inherit;
      }
    }
		// .todos__content
		&__content {
      width: 100%;
      background: inherit;
      input {
        width: 100%;
        transition: all 0.3s ease 0s;
        font-size: 16px;
        font-weight: 500;
          &:hover{
            color: #0284c7; 
          }
      }
    }
		// .todos__actions
		&__actions {
      // background: inherit;
    }
		// .todos__delete
		&__delete {
      cursor: pointer;
      padding: 5px;
       path {
        fill: #0ea5e9;
       }
    }
    // .todos__empty
    &__empty {
      display: flex;
      justify-content:center;
      align-items: center;
      color: #6a717b;
    }
}
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(-150px);
}
</style>
