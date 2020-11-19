<template>
    <v-main class="list">
        <link href="https://cdn.jsdelivr.net/npm/@mdi/font@5.x/css/materialdesignicons.min.css" rel="stylesheet">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
        
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="primary" dark @click="historyDialog = true">
                    TO DO History 
                </v-btn>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip  class="ma-2" :color="Color(item.priority)" label outlined >{{ item.priority }}</v-chip>
                    </td>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="mr-2" @click="editItem(item)">
                        Edit
                    </v-btn>
                    <v-btn small class="mr-2" @click="deleteItem(item)">
                        Delete
                    </v-btn>
                </template>
                <template v-slot:[`item.checkbox`]="{item}">
                    <input type="checkbox" v-model="CheckedTask" :value="item.task">
                </template>
            </v-data-table>
        </v-card>
        <v-dialog v-model="historyDialog" persistent max-width="600px">
            <v-card>
                <v-data-table :headers="headers" :items="history" :search="search"></v-data-table>
                <v-spacer></v-spacer>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
 

        <v-card v-if="CheckedTask.length" persistent max-width="600px">
            <v-card-title primary-title>
                Hapus
            </v-card-title>
            <v-card-text>
                <li v-for="item in CheckedTask" v-bind:key="item.task">
                        {{ item }} 
                </li>
            </v-card-text>
            <v-card-actions>
                <v-btn color="red" text @click="dialogDeleteChecked = true"> Hapus </v-btn>  
            </v-card-actions>
        </v-card>

        <v-dialog v-model="dialogDeleteChecked" persistent max-width="600px"  >
            <v-card>
                <v-card-title>
                    <p>Hapus TO DO ?</p>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="red" text @click="DeleteCheckedItem"> Ya </v-btn>
                    <v-btn color="green" text @click="cancel"> Tidak </v-btn>            
                </v-card-actions>
            </v-card>
        </v-dialog>
       
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form TO DO</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
 
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="update" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Edit TO DO</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
 
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="UpdateTodo">
                        Edit
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="Delete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Ingin menghapus To DO List ?</span>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="red" text @click="cancel">
                        Tidak
                    </v-btn>
                    <v-btn color="success" text @click="DeleteTodo">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>

<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            historyDialog: false,
            Delete: false,
            update: false,
            CheckedTask: [],
            dialogDeleteChecked: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
                { text: "", value: "checkbox"},
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
            ],
            history: [
        
            ],
            formTodo: {
                task: null,
                priority: null,
                note: null,
            },

        };
    },
    methods: {
        save() {
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
            this.update = false;
            this.Delete = false;
            this.DeletedCheck = false;
            this.historyDialog= false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        Color(priority){
            if(priority == "Penting"){
                return 'red';
            }else if(priority == "Tidak penting"){
                return 'green';
            }else{
                return 'blue';
            }
        },
       editItem(item){
            this.update = true
            this.formTodo = item
        },
        UpdateTodo(){         
            this.todos[this.todos.indexOf(this.formTodo)].task = this.formTodo.task;
            this.todos[this.todos.indexOf(this.formTodo)].priority = this.formTodo.priority;
            this.todos[this.todos.indexOf(this.formTodo)].note = this.formTodo.note;  
            this.update = false;       
        },
        deleteItem(item){
            this.Delete = true;
            this.formTodo = item;
        },
        DeleteTodo(){
            this.Delete = false;
            this.history.push(this.formTodo);
            this.todos.splice(this.todos.indexOf(this.formTodo),1);
        },

        DeleteCheckedItem(){
            for( var i = 0 ; i < this.CheckedTask.length ; i++){
                const position = this.todos.indexOf(this.CheckedTask[i+1])
                this.todos.splice(position, 1);
            }
            this.CheckedTask=[];
            this.history.push(this.formTodo);
            this.dialogDeleteChecked = false;
        }
    },
};
</script>