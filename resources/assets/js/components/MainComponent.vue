<template>
    <div>
        <h1 v-if="!edit">Add a Contact</h1>
        <h1 v-else>Update Contact</h1>
        <form action="3" @submit.prevent="edit ? updateContact(contact.id) : addContact()">
            <div class="form-group">
                <label for="name">Name</label>
                <input type="text" v-model="contact.name" class="form-control">
            </div>
            <div class="form-group">
                <label for="email">Email</label>
                <input type="text" v-model="contact.email" class="form-control">
            </div>
            <div class="form-group">
                <label for="phone">Phone</label>
                <input type="text" v-model="contact.phone" class="form-control">
            </div>
            <div>
                <input type="submit" v-if="!edit" class="btn btn-primary mt-3" value="Add Contact">
                <input type="submit" v-else class="btn btn-secondary mt-3" value="Update Contact">
            </div>
        </form>
        <div>
            <h3 class="mt-5" v-if="lists.length > 0">Your Contacts:</h3>
            <div class="card mb-3" v-for="list in lists">
                <div class="card-body">
                    <h5>{{list.name}} <span class="badge badge-danger">{{list.phone}}</span> </h5>
                    <p><strong>Email:</strong> {{list.email}}</p>
                    <button class="btn btn-secondary" @click="editContact(list.id)">Edit</button>
                    <button class="btn btn-danger" @click="deleteContact(list.id)">Delete</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                contact:{
                    id:'',
                    name:'',
                    email:'',
                    phone:''
                },
                lists:[],
                edit: false
            }
        },
        methods:{
            fetchContacts(){
                axios.get('contacts')
                    .then(res=>{
                        console.log(res);
                        this.lists = res.data;
                    })
                    .catch(err=>{
                        console.log(err);
                    });
                },
            addContact(){
                axios.post('contacts', this.contact)
                .then(res => {
                    console.log('Adding Contact');
                    this.contact.name = '';
                    this.contact.email = '';
                    this.contact.phone = '';
                    this.fetchContacts();
                })
                .catch(err=>{
                    console.log(err)
                });
            },
            editContact(id){
                axios.get('contacts/' + id +'/edit')
                .then(res=>{
                    console.log(res);
                    this.contact.id = id;
                    this.contact.name = res.data.name;
                    this.contact.email = res.data.email;
                    this.contact.phone = res.data.phone;
                    this.edit = true;
                })
                .catch(err=>{
                    console.log(err);
                });
            },
            updateContact(id){
                axios.patch('contacts/' + id, this.contact)
                .then(res=>{
                    this.contact.id = '';
                    this.contact.name = '';
                    this.contact.email = '';
                    this.contact.phone = '';
                    this.edit = false;
                    this.fetchContacts();
                })
                .catch(err=>{
                    console.log(err);
                });
            },
            deleteContact(id){
                axios.delete('contacts/' + id)
                .then(res=>{
                    this.fetchContacts();
                })
                .catch(err=>{
                    console.log(err);
                });
            }

        },
        mounted() {
            this.fetchContacts();
        }
    }
</script>
