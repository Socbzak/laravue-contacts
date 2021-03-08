<template>
    <app-layout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                CONTACTS
            </h2>
        </template>

        <div class="fixed inset-0 text-center z-10" v-if="isOpen">

            
            <div class="fixed inset-0 transition-opacity bg-blue-500 opacity-40"></div>

            <div class="inline-block bg-white rounded-lg text-left overflow-hidden shadow-xl transform m-8 sm:max-w-md sm:w-full">
                <form>
                    <div class="bg-white p-4">
                        <h2 class="text-xl">Contact Form</h2>
                        <div class="mb-4">
                            <label for="name">Name:</label>
                            <input id="name" type="text" v-model="form.name" placeholder="Enter Name" class="rounded w-full"/>
                        </div>
                        <div class="mb-4">
                            <label for="phone">Phone:</label>
                            <input id="phone" type="text" v-model="form.phone" placeholder="Enter Phone" class="rounded w-full"/>
                        </div>
                        <div class="mb-4">
                            <label for="email">E-mail:</label>
                            <input id="email" type="email" v-model="form.email" placeholder="Enter E-mail" class="rounded w-full"/>
                        </div>
                        <div class="mb-4">
                            <label for="birthday">Birthday:</label>
                            <input id="birthday" type="date" v-model="form.birthday" placeholder="Enter Birthday" class="rounded w-full"/>
                        </div>
                        <div class="mb-4">
                            <label for="type">Type:</label>
                            <select id="type" v-model="form.type" class="rounded w-full">
                                <option v-for="option in types" v-bind:value="option.value">
                                    {{ option.text }}
                                </option>
                            </select>
                        </div>
                        <div>
                            <button class="bg-red-500 text-white p-2 rounded" @click.prevent="closeModal()"> Cancel </button>
                            <button class="ml-2 bg-blue-500 text-white p-2 rounded" @click.prevent="save()" v-if="!form.id"> Save </button>
                            <button class="ml-2 bg-green-500 text-white p-2 rounded" @click.prevent="update()" v-if="form.id"> Update </button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
        
        <div class="p-6">
            <div class="bg-white shadow rounded p-6">
        <button class="bg-blue-500 text-white p-2 rounded" @click="openModal()"> Create New Contact </button>
        
                <table class="table-fixed w-full mt-4">
                    <thead>
                        <tr class="bg-blue-100">
                            <th class="p-2 w-20">No.</th>
                            <th class="p-2">Name</th>
                            <th class="p-2">Phone</th>
                            <th class="p-2">E-mail</th>
                            <th class="p-2">Birthday</th>
                            <th class="p-2">Type</th>
                            <th class="p-2">Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="row in data">
                            <td class="p-2 border">{{row.id}}</td>
                            <td class="p-2 border">{{row.name}}</td>
                            <td class="p-2 border">{{row.phone}}</td>
                            <td class="p-2 border">{{row.email}}</td>
                            <td class="p-2 border">{{row.birthday}}</td>
                            <td class="p-2 border">{{getType(row.type)}}</td>
                            <td class="p-2 border">
                                <button class="bg-blue-500 text-white p-2 rounded" @click="edit(row)" >EDIT</button>
                                <button class="bg-red-500 text-white p-2 rounded" @click="deleteRow(row)" >DELETE</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </app-layout>
</template>

<script>
    import AppLayout from '@/Layouts/AppLayout'
    export default {
        components:{
            AppLayout
        },
        props: ['data'],
        data () {
            return {
                isOpen: false,
                form: {
                    name: null,
                    phone: null,
                    email: null,
                    birthday: null,
                    type: 1,
                },
                types: [
                    { text: 'Pessoal'   , value: '1' },
                    { text: 'Celular'   , value: '2' },
                    { text: 'Casa'      , value: '3' },
                    { text: 'Comercial' , value: '4' },
                    { text: 'Principal' , value: '5' },
                    { text: 'Secundário', value: '6' },
                    { text: 'Fax'       , value: '7' },
                    { text: 'Outro'     , value: '8' }
                ]
            }
        },
        methods:{
            getType(value){
                var type = this.types.filter(t => t.value.indexOf(value) > -1);
                return type[0].text;
            },
            edit(data){
                this.form = Object.assign({},data);
                this.openModal();
                // console.log('EDIT >>>>>> ', this.form);
            },
            update(){
                this.$inertia.put('/contacts/'+this.form.id,this.form);
                this.closeModal();
            },
            deleteRow(data){
                this.$inertia.delete('/contacts/'+data.id, 
                    {
                        onBefore: () => confirm('Are you sure you want to delete?')
                    }
                );
                this.closeModal();
            },
            save(){
                this.$inertia.post('/contacts',this.form ,
                    {
                        onSuccess: () => alert('SAVED!')
                    })

                this.closeModal();
            },
            openModal(){
                this.isOpen = true;
            },
            closeModal(){
                this.reset();
                this.isOpen = false;
            },
            reset(){
                this.form.name = null;
                this.form.phone = null;
                this.form.email = null;
                this.form.birthday = null;
                this.form.type = 1;
            }
        }
    }
</script>