<template>
        <v-main class="list">
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
                         Delete TODO List
                    </v-btn>
                    <v-btn color="success" dark @click="dialog = true">
                        Tambah
                    </v-btn>
                </v-card-title>
                <v-data-table :headers="headers" :items="todos" :search="search">
                    <template v-slot:[`item.actions`]="{ item }">
                        <v-btn small class="mr-2" @click="editItem(item)">
                            edit
                        </v-btn>
                        <v-btn small @click="deleteItem(item)">
                            delete
                        </v-btn>
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

            <v-dialog v-model="dialog" persistent max-width="600px">
                <v-card>
                    <v-card-title>
                        <span class="headline">Form Todo</span>
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

            <v-dialog v-model="deleteDialog" persistent max-width="350px">
            <v-card>
                <v-card-title>
                    <p>Ingin menghapus TO DO List ?</p>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="red" text @click="deleteFromDialog"> Ya </v-btn>
                    <v-btn color="green" text @click="cancel"> Tidak </v-btn>            
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="update" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Edit TO DO </span>
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
                    <v-btn color="blue darken-1" text @click="editFromDialog">
                        Save
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
            update: false,
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
            this.deleteDialog = false;
            this.historyDialog = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        deleteItem(item) {
            this.deleteDialog = true
            this.formTodo = item
        },
        deleteFromDialog() {
            this.deleteDialog = false;
            this.history.push(this.formTodo);
            this.todos.splice(this.todos.indexOf(this.formTodo), 1);
        },
        editItem(item) {
            this.update = true
            this.formTodo = item
        },
        editFromDialog() {
            this.todos[this.todos.indexOf(this.formTodo)].task = this.formTodo.task;
            this.todos[this.todos.indexOf(this.formTodo)].priority = this.formTodo.priority;
            this.todos[this.todos.indexOf(this.formTodo)].note = this.formTodo.note;  
            this.update = false;     
        },

    },
};
</script>