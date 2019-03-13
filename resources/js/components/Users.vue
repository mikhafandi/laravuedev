<template>
    <div class="container">
        <div class="row mt-5">
        <div class="col-md-12">
          <div class="card">
            <div class="card-header">
              <h3 class="card-title">Responsive Hover Table</h3>
              <button class="btn btn-success "  @click= "newModal">Add New
                  <i class="fa fa-user-plus fa-fw"></i>
              </button>
              <div class="card-tools">
                  
                <div class="input-group input-group-sm" style="width: 150px;">
                  <input type="text" name="table_search" class="form-control pull-right" placeholder="Search">
                  <div class="input-group-btn">
                    <button type="submit" class="btn btn-default"><i class="fa fa-search"></i></button>
                  </div>
                </div>
                
              </div>
            </div>
            <!-- /.box-header -->
            <div class="card-body table-responsive no-padding">
              <table class="table table-hover">
                <tbody><tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Registered at</th>
                  <th>Modify</th>
                </tr>
                
                <tr v-for="user in users" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>                  
                  <td>{{user.type | upText}}</td>
                  <td>{{user.created_at | myDate}}</td>
                  <td>
                      <a href="#" @click= "editModal(user)">
                          <i class="fa fa-edit"></i>
                      </a>|
                      <a href="#" @click= "deleteUser(user.id)" >
                          <i class="fa fa-trash red"></i>
                      </a>
                  </td>
                </tr>
                
              </tbody></table>
            </div>
            <!-- /.box-body -->
          </div>
          <!-- /.box -->
        </div>
      </div>

    <!-- Modal -->
    <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
        <div class="modal-header">
            <h5 class="modal-title" id="addNewLabel">Add User</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
            </button>
        </div>

        <form @submit.prevent="createUser">

        <div class="modal-body">
            <div class="form-group">
                <label>Username</label>
                <input id="name" v-model="form.name" type="text" name="name"
                    placeholder="name"
                    class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                <has-error :form="form" field="name"></has-error>
            </div>
            <div class="form-group">
                <label>Email</label>
                <input id="email" v-model="form.email" type="text" name="email"
                    placeholder="Email Address"
                    class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                <has-error :form="form" field="email"></has-error>
            </div>
            <div class="form-group">
                <label>Bio</label>
                <input id="bio" v-model="form.bio" type="text" name="bio"
                    placeholder="short bio"
                    class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }">
                <has-error :form="form" field="bio"></has-error>
            </div>
            <div class="form-group">
                <label>Type</label>
                <select id="type" v-model="form.type" name="type"
                    placeholder="user type"
                    class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">

                    <option value=""> Select User Role </option>
                    <option value="admin"> Admin </option>
                    <option value="user"> User </option>
                    <option value="author"> Author </option>
                </select>
                <has-error :form="form" field="type"></has-error>
            </div>
            <div class="form-group">
                <label>Password</label>
                <input id="password" v-model="form.password" type="password" name="password"
                    placeholder="password"
                    class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                <has-error :form="form" field="password"></has-error>
            </div>
        </div>
        <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
            <button type="submit" class="btn btn-primary">Save</button>
        </div>

        </form>

        </div>
    </div>
    </div>
      
    </div>
</template>

<script>
    export default {
        data (){
            return{
                users : {},
                
                form: new Form({
                    name:'',
                    email:'',
                    password:'',
                    type:'',
                    bio:'',
                    photo:''
                })
            }
        },
        methods: {
            newModal(){
                this.form.reset();
                $('#addNew').modal('show');
            },
            editModal(user){
                this.form.reset();
                $('#addNew').modal('show');
                this.form.fill(user);
            },
            deleteUser(id){
                swal.fire({
                title: 'Are you sure?',
                text: "You won't be able to revert this!",
                type: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    //send req to server
                    if (result.value){
                    this.form.delete('api/user/'+id).then(()=>{

                        swal(
                        'Deleted!',
                        'Your file has been deleted.',
                        'success'
                        )
                        Fire.$emit('AfterCreate');
                    }).catch(() =>{
                        swal("Failed","sumting wong","warning");
                    });               
                    
                    }                 
                })
            },
            loadUsers(){
                axios.get("api/user").then(({data}) => (this.users = data.data));
            },
            createUser(){
                this.$Progress.start();
                this.form.post('api/user')
                .then(()=>{
                    //call proses after creation
                    Fire.$emit('AfterCreate');

                    $('#addNew').modal('hide');
                    
                    toast.fire({
                        type: 'success',
                        title: 'User Created Success'
                    })
                    
                    this.$Progress.finish();

                })
                .catch(()=>{

                })
            }
        },  
        created() {
            this.loadUsers();
            Fire.$on('AfterCreate',() => {
                this.loadUsers();
            });
        }
    }
</script>
