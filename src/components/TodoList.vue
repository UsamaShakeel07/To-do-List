<template>
<div id="content todo-item" class="main-content-wrapper">
    <h1 class="flex todo justify-center text-center text-tertiary-500 pt-20 text-6xl">TODO LIST</h1>
    <div class="z-10 field flex justify-center w-full items-center content-center pt-10">
        <input v-model="todo" v-on:keyup.enter="submitTodo" type="text" class="rounded-l-3xl w-60 h-10 text-center justify-center text-white form-control bg-gray-800 flex z-10" placeholder="Enter Todo">
        <div class="gap-2 flex">
            <button v-on:click="submitTodo"  class="rounded-r-3xl add-item h-10 w-20 justify-center bg-gray-600 text-white flex z-10">
                <plus-thick class="h-full"></plus-thick>
                <p class="pt-2">Add</p>
            </button>
            <button class="rounded-3xl add-item py-2 px-3 justify-center bg-red-500 hover:bg-red-600 text-white flex z-10" @click="deleteAllTodo(index)">
                <p>Delete All</p>
            </button>
        </div>
    </div>
    <div class="flex flex-col pt-5 pb-20">
        <div id='todo-item' class="w-full bg-gray-600 text-white text-2xl text-center flex justify-center" v-if="!todos || todos.length == 0">
            List is null or empty.... better do something to fill it here
        </div>
        <div class="overflow-x-auto w-screen">
            <div class="py-2 inline-block min-w-full">
                <div class="overflow-hidden">
                    <table class="w-full">
                        <thead class="bg-gray-800 text-white border-b">
                            <tr v-if="!todos || todos.length !== 0">
                                <th scope="col" class="text-sm font-medium py-4">
                                    S.no
                                </th>
                                <th scope="col" class="text-sm font-medium py-4">
                                    Status
                                </th>
                                <th scope="col" class="text-sm font-medium py-4">
                                    Description
                                </th>
                                <th scope="col" class="text-sm font-medium py-4">
                                    ID
                                </th>
                                <th scope="col" class="text-sm font-medium py-4">
                                    Edit
                                </th>
                                <th scope="col" class="text-sm font-medium py-4">
                                    Delete
                                </th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(todo, index) in filteredtodos" :key="index" class="bg-gray-100 border-b">
                                <td class="py-4 whitespace-nowrap text-sm font-medium text-gray-900">{{todo.number}}</td>
                                <td class="text-sm text-gray-900 font-light py-4 whitespace-nowrap">
                                    <span :class="{'text-primary-200': todo.status === 'to-do',
                                  'text-secondary-500': todo.status === 'in-progress',
                                  'text-primary-500': todo.status === 'finished'}" @click="changeStatus(index)" class="pointer">{{ firstCharUpper(todo.status) }}</span>
                                </td>
                                <div>
                                <td class="text-sm text-gray-900 font-light py-4 whitespace-nowrap">
                                        <span :class="{'finished': todo.status === 'finished'}">
                                            {{todo.description}}
                                        </span>
                                    </td>
                                </div>
                                <td class="text-sm text-gray-900 font-light py-4 whitespace-nowrap">
                                    {{todo.id}}
                                </td>
                                <td class="text-sm text-gray-900 font-light py-4 whitespace-nowrap">
                                    <div @click="editTodo(index)" class="flex z-10 justify-center"><button class="flex z-10">
                                            <square-edit-outline class="text-xl"></square-edit-outline>
                                        </button></div>
                                </td>
                                <td class="z-10 text-sm text-gray-900 font-light py-4 whitespace-nowrap">
                                    <div @click="deleteTodo(index)" class="flex z-10 text-red-500 hover:text-red-600 justify-center"><button class="flex z-10">
                                            <delete-outline class="text-xl"></delete-outline>
                                        </button></div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import {
    PlusThick
} from 'mdue';
import {
    SquareEditOutline
} from 'mdue';
import {
    DeleteOutline
} from 'mdue';

export default {
    name: 'TodoList',
    components: {
        PlusThick,
        SquareEditOutline,
        DeleteOutline
    },

    data() {
        return {
            todo: '',
            editedTodo: null,
            availableStatuses: ['to-do', 'in-progress', 'finished'],
            todos: [{
                    number: 1,
                    description: 'Steal bananas from the store.',
                    status: 'to-do',
                    id: '111'
                },
                {
                    number: 2,
                    description: 'Eat 1kg chocolate in 1 hour.',
                    status: 'in-progress',
                    id: '112'
                },
            ],
            
            

        }
    },
    computed: {
        filteredtodos() {
            return this.todos.filter(todo => 
                todo.description.toLowerCase().includes(this.todo.toLowerCase())
            );
        },
    },
    methods: {
        async submitTodo() {
          if (this.todo.length === 0) return; 
            if (this.editedTodo === null) {
                var checkIndex = await this.todos.findIndex((x) => {
                    return x.description == this.todo   
                });
                if(checkIndex == -1){
                    this.todos.push({
                        number: 3,
                        description: this.todo,
                        status: 'to-do',
                        id: '113'
                    });
                } else {
                    alert('already added')
                }
            } else {
                this.todos[this.editedTodo].description = this.todo;
                this.editedTodo = null;
            }
            this.todo = '';
        },
        deleteAllTodo(index) {
            this.todos.splice(index);
        },
        deleteTodo(index) {
            this.todos.splice(index, 1);
        },
        editTodo(index) {
            this.todo = this.todos[index].description;
            this.editedTodo = index;
        },
        changeStatus(index) {
            let newIndex = this.availableStatuses.indexOf(this.todos[index].status);
            if (++newIndex > 2) newIndex = 0;
            this.todos[index].status = this.availableStatuses[newIndex];
        },
        firstCharUpper(str) {
            return str.charAt(0).toUpperCase() + str.slice(1);
        },
    },

}
</script>

<style>
.pointer {
    cursor: pointer;
}

.finished {
    text-decoration: line-through;
}
</style>
