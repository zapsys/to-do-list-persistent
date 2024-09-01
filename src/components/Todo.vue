<template>
    <v-container class="border-sm rounded-lg p-3 elevation-12" fill-height>
        <v-row>
            <v-col class="d-flex align-top">
                <template v-if="editTitle">
                    <v-text-field type="text" variant="underlined" placeholder="Novo título" name="title" id="title">
                        <template v-slot:append>
                            <v-icon color="blue" @click="saveTitle" size="32">mdi-content-save</v-icon>
                        </template>
                    </v-text-field>
                </template>
                <template v-else>
                    <h2 class="mb-3 mr-3 d-inline text-uppercase">{{ title }}</h2>
                    <v-icon @click="changeTitle">mdi-pencil</v-icon>
                </template>
            </v-col>
        </v-row>
        <v-form>
            <v-row class="d-flex align-center">
                <v-col>
                    <v-text-field type="text" variant="underlined" v-model="currentTodo" placeholder="Descrição/item"
                        @keyup.enter="addTodo">
                        <template v-slot:append>
                            <v-icon color="green" @click.prevent="addTodo" size="32">mdi-plus</v-icon>
                        </template>
                    </v-text-field>
                </v-col>
            </v-row>
        </v-form>
        <v-row v-for="todo in filteredTodos" :key="todo.text" @click="toggleTodo(todo)">
            <v-col>
                <v-checkbox v-model="todo.done">
                    <template v-slot:label>
                        <del v-if="todo.done">
                            {{ todo.text }}
                        </del>
                        <span v-else>
                            {{ todo.text }}
                        </span>
                    </template>
                    <template v-slot:append>
                        <v-icon color="red" @click="delTodo(todo)">mdi-delete</v-icon>
                    </template>
                </v-checkbox>
            </v-col>
        </v-row>
    </v-container>
</template>
<script>
export default {
    data: () => ({
        title: 'título',
        editTitle: false,
        currentTodo: '',
        editedTodo: null,
        todos: []
    }),
    computed: {
        filteredTodos() {
            return this.todos.filter(
                todo => todo.text.toLowerCase().match(this.currentTodo.toLowerCase())
            );
        },
    },
    mounted() {
        if (localStorage.getItem("todos")) {
            this.todos = JSON.parse(localStorage.getItem("todos"))
        }
        if (localStorage.getItem("title")) {
            this.title = localStorage.getItem("title")
        }
    },
    watch: {
        todos: {
            handler() {
                localStorage.setItem("todos", JSON.stringify(this.todos))
                localStorage.setItem("title", this.title)
            },
            deep: true
        }
    },
    directives: {
        focus: {
            inserted(el) {
                el.focus()
            }
        }
    },
    methods: {
        addTodo() {
            if (this.currentTodo === '') return alert('Informe um item ou descrição!')
            else if (!this.isValidInput()) return alert('Item ou descrição já inserida!')
            else {
                this.todos.push({
                    text: this.currentTodo,
                    done: false
                });
            }
            this.currentTodo = ''
            this.sortTodos()
        },
        toggleTodo(todo) {
            todo.done = !todo.done
            this.sortTodos()
        },
        editTodo(todo) {
            this.editedTodo = todo
        },
        delTodo(todo) {
            this.todos = this.todos.filter(el => el.text !== todo.text)
        },
        sortTodos() {
            this.todos.sort((a, b) => a.done - b.done)
        },
        isValidInput() {
            return !(!this.currentTodo.trim() || this.checkIfTodoExists())
        },
        checkIfTodoExists() {
            return this.todos.some((todo) => todo.text === this.currentTodo.trim())
        },
        changeTitle() {
            this.editTitle = true
        },
        saveTitle() {
            this.title = title.value
            localStorage.setItem("title", this.title)
            this.editTitle = false
        }
    }
}
</script>