<template>
    <div>
        <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo"
        @keyup.enter="addTodo" >
        <transition-group enter-active-class="animated bounceInLeft" leave-active-class="animated bounceOutLeft">
            <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
                <div class="todo-item-left">
                    <input type="checkbox" v-model="todo.completed" class="chkBox">
                    <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-lable" :class="{completed : todo.completed}">{{ todo.title }}</div>
                    <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneTodo(todo)" @keyup.enter="doneTodo(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
                </div>
                
                <div class="remove-item" @click="removeTodo(index)">
                    &times;
                </div>
            </div>
        </transition-group>

        <div class="extra-container">
            <div class="chkAll"><label><input type="checkbox" class="chkBox" :checked="!anyLeft" @change="checkAllTodos()">Check All</label></div>
            <div class="count">{{ remaining }} Items left</div>
        </div>

        <div class="extra-container" >
            <div class="chkAll">
                <button :class="{active: filter=='all'}" @click="filter='all'">All</button>
                <button :class="{active:filter=='completed'}" @click="filter='completed'">Completed</button>
                <button :class="{active:filter=='active'}" @click="filter='active'">Active</button>
            </div>
            <div class="count">
                <transition name="fade">
                    <button v-if="showClearCompleatedButton" @click="clearCompleted">Clear Completed</button>
                </transition>
            </div>
        </div>

    </div>
    </template>
    
    <script>
    const focus = {
        mounted: (el) => el.focus()
    }
    export default {
      name: 'todo-list',
      props: {    
      },
    data () { 
            return {
                newTodo : "",
                idForTodos : 3,
                beforeEditCache : "",
                filter : 'all',
                todos : [
                    {
                        "id" : 1,
                        "title" : "Finish vue project",
                        "completed" : false,
                        "editing" : false
                    },
                    {
                        "id" : 2,
                        "title" : "Complete hacker rank test",
                        "completed" : false,
                        "editing" : false
                    }
                ]
            };
        },
        directives : {
            focus 
        },
        computed : {
            remaining(){
                return this.todos.filter(todo => !todo.completed).length
            },

            anyLeft(){
                return this.remaining != 0
            },
            todosFiltered(){
                if (this.filter=='all'){
                    return this.todos
                } else if (this.filter=='completed'){
                    return this.todos.filter(todo => todo.completed)
                } else if (this.filter=='active'){
                    return this.todos.filter(todo => !todo.completed)
                }
                return this.todos
            },
            showClearCompleatedButton(){
                return this.todos.filter(todo => todo.completed).length > 0
            }
        },
        methods : {
            
            addTodo() {
                if(this.newTodo.trim().length == 0){
                    return
                }

                this.todos.push({
                    id: this.idForTodos,
                    title: this.newTodo,
                    completed: false,
                    editing: false
                });

                this.newTodo = ''
                this.idForTodos++
            },
            editTodo(todo){
                this.beforeEditCache=todo.title;
                todo.editing = true
            },

            cancelEdit(todo){
                todo.title= this.beforeEditCache
                todo.editing = false
            },

            doneTodo(todo){
                if(todo.title.trim() == ''){
                    todo.title= this.beforeEditCache
                }
                todo.editing = false
            },

            removeTodo(index){
                this.todos.splice(index, 1)
            },

            checkAllTodos(){
                this.todos.forEach(todo => todo.completed = event.target.checked)
            },

            clearCompleted(){
                this.todos = this.todos.filter(todo => !todo.completed)
            }
        }
    }
    </script>
    
    <!-- Add "scoped" attribute to limit CSS to this component only -->
    <style >
        @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css");

        .todo-input {
            width: 100%;
            padding: 10px 10px;
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
            animation-duration: 1s;
        }

        .remove-item{
            cursor: pointer;
            margin-right: 0px;
            
        }
        .remove-item:hover{
                color: black;
            }

        .todo-item-left{
            display : flex;
            align-items: center;
            margin-left: 0px;
        }

        .todo-item-lable{
            padding: 10px 10px 10px 0px;
            /* margin-left: 12px; */
            border: 1px solid white;
        }

        .todo-item-edit{
            padding: 10px;
            margin-left: 12px;
            border: 1px solid #ccc;
            width: 100%;
            color: #2c3e50;
            font-size: 24px;
            font-family: Avenir, Helvetica, Arial, sans-serif;
            /* font-family: 'Avenir', Arial, Helvetica, sans-serif; */
        }
        .todo-item-edit:focus{
                outline: none;
        }

        .completed{
            text-decoration: line-through;
            color:grey;
        }

        .extra-container{
            display: flex;
            align-items: center;
            justify-content: space-between;
            font-size: 16px;
            border-top: 3px solid lightgrey;
            padding-top: 14px;
            margin-bottom: 14px;
        }

        button {
            font-size: 14px;
            background-color: white;
            appearance: none;
            -webkit-appearance: none;
            -moz-appearance: none;
            margin-left: 4px;
            border-color: lightgray;


        }
        button:hover{
            background: lightgreen;
        }
        button:focus{
            outline: none;
        }
        .active{
            background:lightgreen;
        }

        .chkBox{
            margin-right: 10px
        }
        .chkAll{
            margin-left: 2px;
        }
        .count{
            margin-right: 2px;
        }

        .fade-enter-active , .fade-leave-active {
            transition: opacity .2s;
        }
        .fade-enter, .fade-leave-to{
            opacity: 0;
        }
    </style>