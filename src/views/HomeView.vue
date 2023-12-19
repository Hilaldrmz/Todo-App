<template>
    <main>
        <header>
            <h1>{{ header }}</h1>
        </header>
        <form @submit.prevent="addTodo">
            <input v-model="newTodo" type="text" name="newTodo" id="newTodo" placeholder="New todo">
            <button type="submit" name="button"><font-awesome-icon :icon="['fas', 'plus']"
                    style="color: var(--color-text);" /></button>
        </form>
        <!-- <button @click="allDone" type="button" name="button">Mark All Done</button> -->
        <ul>
            <li v-for="todo in todos" :key="todo.text">
                <input type="checkbox" v-model="todo.done" @click="updateLocalStorage(todo)">
                <span :class="{ done: todo.done }">{{ todo.text }}</span>
                <font-awesome-icon @click="removeTodo(todo)" :icon="['far', 'trash-can']" style="color: #c40808;" />
            </li>
        </ul>
    </main>
</template>

<script setup>
import { ref, onMounted } from 'vue';

onMounted(() => { getTodosFromLocalStorage() })
const header = "todo list";
const newTodo = ref('');
const todos = ref([]);

function addTodo() {
    if (newTodo.value.trim().length > 0) {

        todos.value.push({
            id: Math.floor(Math.random() * 100000),
            text: newTodo.value,
            done: false
        });
        newTodo.value = '';
        saveTodosToLocalStorage();
    }
}

function removeTodo(todo) {
    const todoIndex = todos.value.indexOf(todo);
    todos.value.splice(todoIndex, 1);
    saveTodosToLocalStorage();
}

function allDone() {
    todos.value.forEach(todo => {
        todo.done = true;
    })
}

function saveTodosToLocalStorage() {
    localStorage.setItem('todos', JSON.stringify(todos.value));
}
const getTodosFromLocalStorage = () => {
    const savedTodos = JSON.parse(localStorage.getItem('todos'));
    if (savedTodos) {
        todos.value = savedTodos
    };
}

function updateLocalStorage(todo) {
    const todoIndex = todos.value.findIndex(item => item.id === todo.id);
    if (todoIndex !== -1) {
        todos.value[todoIndex].done = !todos.value[todoIndex].done;
        saveTodosToLocalStorage();
    }
}

</script>

<style lang="scss" scoped>
@import url('https://fonts.googleapis.com/css2?family=Homemade+Apple&display=swap');

main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    header {
        h1 {
            font-family: 'Homemade Apple', cursive;
            font-size: 3em;
        }
    }

    form {
        margin: 10px;

        input {
            border: none;
            border-bottom: 3px dashed #D6CBC7;
            padding: 0 10px 0 10px;
            background: none;
            color: var(--color-text);

            &::placeholder {
                font-family: 'Homemade Apple', cursive;
            }

            &:focus {
                outline: none;
            }

        }

        button {
            background: none;
            border: none;
        }
    }

    li {
        display: flex;
        list-style-type: none;
        align-items: center;

        input {
            appearance: none;
            border: 2px solid #D6CBC7;
            border-radius: 50%;
            color: green;
            height: 2em;
            position: relative;
            width: 2em;
            vertical-align: -5px;
            margin: 10px;

            &::before {
                color: rgb(238, 130, 130);
                content: "✔";
                font-size: 2em;
                position: absolute;
                right: -3px;
                top: -0.3em;
                visibility: hidden;
            }

            &:checked::before {
                visibility: visible;
            }

            &:disabled {
                background: darkgrey;
                border-color: rgb(175, 129, 175);
                color: gray;
            }
        }

        span {
            background-color: #D6CBC7;
            border-radius: 15px;
            display: flex;
            width: 50dvw;
            padding: 0 15px;
            color: black;
            font-weight: 300;

            &.done {
                text-decoration: line-through;
                color: grey;
                font-style: italic;
                font-weight: 200;
            }
        }

        input+span {
            margin: 0 5px;
        }

    }
}</style>