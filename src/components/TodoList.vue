<template>
    <div>
        <input
            type="text" class="todo-input"
            placeholder="what to do next"
            @keyup.enter="addTodo"
            v-model="newTodo">
        <div
            v-for="todo, index in todosFiltered"
            :key="todo.id"
            class="todo-item">
            <div class="todo-item-left" >
                <input type="checkbox" v-model="todo.is_complete">
                <div
                    class="todo-item-lable" v-if="!todo.is_editing"
                    @dblclick="changeEditTodoStatus(todo)"
                    :class="{ completed: todo.is_complete }">
                    {{todo.title}}
                </div>
                <input
                    type="text" class="todo-item-edit"
                    v-else v-model="todo.title"
                    @blur="editDone(todo)"
                    @keyup.enter="changeEditTodoStatus(todo)"
                    v-focus
                    @keyup.esc="cancelEdit(todo)">
            </div>
            <div class="remove-item" @click="removeTodo(index)">
                &times;
            </div>
        </div>

        <div class="extra-container">
            <div class="checkall-todo">
                <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">
                <div class="checkall-lable">Check All</div>
            </div>
            <div>
                {{ remaining }} items left
            </div>
        </div>
        
        <div class="extra-container">
            <div>
                <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
                <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
                <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
            </div>
            <div>
                <button class="clear-completed" @click="clearCompleted">Clear Completed</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name:'todo-list',
    data(){
        return{
            newTodo: '',
            newTodoId: 3,
            newTitle: '',
            editTodoStatus: false,
            beforeEditing: '',
            filter: 'all',
            todos:[
                {
                    'id': 1,
                    'title': 'drink water',
                    'is_complete': false,
                    'is_editing': false,
                },
                {
                    'id': 2,
                    'title': 'finish todo portfolio',
                    'is_complete': false,
                    'is_editing': false
                }
            ]
        }
    },
    computed:{
        remaining(){
            return this.todos.filter(todo=> !todo.is_complete).length;
        },
        anyRemaining(){
            return this.remaining !== 0;
        },
        todosFiltered(){
            if (this.filter == 'all') {
                return this.todos
            }else if(this.filter == 'active'){
                return this.todos.filter(todo => !todo.is_complete)
            }else{
                return this.todos.filter(todo => todo.is_complete)
            }
        }
    },
    directives: {
        focus: {
            // directive definition
            inserted: function (el) {
            el.focus()
            }
        }
    },
    methods:{
        clearCompleted(){
            this.todos = this.todos.filter(todo => !todo.is_complete)
        },
        checkAllTodos(){
            this.todos.forEach((todo) => todo.is_complete = event.target.checked)
        },
        removeTodo(index){
            this.todos.splice(index, 1);
        },
        changeEditTodoStatus(todo){
            if(todo.title.trim().length !== 0){
                this.beforeEditing = todo.title
                todo.is_editing = !todo.is_editing
            }
        },
        editTodo(index){
            if (this.newTitle.trim() !== 0) {
                this.todos[index].title = this.newTitle
            }
        },
        editDone(todo){
            if(todo.title.trim().length == 0){
                todo.title = this.beforeEditing
            }
            todo.is_editing = !todo.is_editing
        },
        cancelEdit(todo){
            todo.is_editing = !todo.is_editing
            todo.title = this.beforeEditing
        },
        addTodo(){
            if (this.newTodo.trim().length !==0) {
                this.todos.push({
                    'id': this.newTodoId,
                    'title': this.newTodo,
                    'is_complete': false,
                    'is_editing': false
                })

                this.newTodo = ''
                this.newTodoId++
            }
        }
    }
}
</script>

<style>
    .todo-input{
        width: 100%;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;
    }

    .todo-input:focus{
        outline: 0;
    }

    .todo-item{
        margin-bottom: 12px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .remove-item{
        cursor: pointer;
        margin-left: 14px;
    }
    .remove-item:hover{
        color: black;
    }
    .todo-item-left{
        display: flex;
        align-items: center;
    }
    .todo-item-lable{
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
    }
    .todo-item-edit{
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
    }
    .todo-item-edit:focus{
        outline: none;
    }
    .completed{
        text-decoration: line-through;
        color: gray;
    }
    .extra-container{
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgray;
        padding-top: 14px;
        margin-bottom: 14px;
    }
    button{
        font-size: 14px;
        background-color: white;
        appearance: none;
        margin-right: 5px ;
        border: 1px solid gray;
    }
    button:hover{
        background: lightgreen;
    }
    button:focus{
        outline: none;
    }
    .active{
        background: lightgreen;
    }
    .checkall-todo{
        display: flex;
    }
</style>