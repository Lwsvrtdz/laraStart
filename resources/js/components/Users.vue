<template>
    <div class="container">
      <div class="row">
        <div class="col-xs-12">
          <div class="box">
            <div class="box-header">
              <h3 class="box-title">Users Table</h3>

              <div class="box-tools">
               <button class="btn btn-success" @click="newModal">Add New<i class="fa fa-user-plus fa-fw"></i></button>
              </div>
            </div>
            <!-- /.box-header -->
            <div class="box-body table-responsive no-padding">
              <table class="table table-hover">
                <tbody><tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Bio</th>
                  <th>Registered Date</th>
                  <th>Modify</th>
                </tr>



                 <tr v-for="user in users.data" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type | upText}}</td>
                  <td>{{user.bio}}</td>
                  <td>{{user.created_at | myDate}}</td>
                  
                  <td>
                  <a href="#">
                  <i class="fa fa-edit" @click="editModal(user)"> </i>
                   </a>
                   /
                    <a href="#" @click="deleteUser(user.id)">
                  <i class="fa fa-trash"> </i>
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
      <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" v-show="!editmode" id="addNewLabel">Add new user</h5>
        <h5 class="modal-title" v-show="editmode" id="addNewLabel">Update user's Info</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <form @submit.prevent="editmode ? updateUser() : createUser()">
      <div class="modal-body">

        <div class="form-group">

      <input v-model="form.name" placeholder="Name" name="name" type="text"
        class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
      <has-error :form="form" field="name"></has-error>
    </div>


        <div class="form-group">

      <input v-model="form.email" placeholder="Email" name="email" type="email"
        class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
      <has-error :form="form" field="email"></has-error>
    </div>


      
  <div class="form-group">
                        <select name="type" v-model="form.type" id="type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                            <option value="">Select User Role</option>
                            <option value="admin">Admin</option>
                            <option value="user">Standard User</option>
                            <option value="author">Author</option>
                        </select>
                        <has-error :form="form" field="type"></has-error>
                    </div>


     <div class="form-group">
                        <textarea v-model="form.bio" name="bio" id="bio"
                        placeholder="Short bio for user (Optional)"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                        <has-error :form="form" field="bio"></has-error>
                    </div>

  <div class="form-group">

      <input v-model="form.password" placeholder="Password" name="password" type="password"
        class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
      <has-error :form="form" field="password"></has-error>
    </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
        <button v-show="editmode" type="submit" class="btn btn-success">Update</button>
        <button v-show="!editmode" type="submit" class="btn btn-primary">Create user</button>
      </div>

      </form>
    </div>
  </div>
</div>
    </div>
</template>

<script>
    export default {
data() {
  
    return {

editmode: false,

     users : {},
form: new Form({
  id: '',
name: '',
email: '',
password: '',
type: '',
bio: '',
photo: ''
})
    }
},

methods: {
updateUser(){
   this.$Progress.start();
 this.form.patch('api/user/'+this.form.id)
 .then(() => {
         
        $('#addNew').modal('hide');

    swal.fire(
      'Updated!',
      'Information has been updated.',
      'success'
    )

  this.$Progress.finish();
     Fire.$emit('AfterCreate');
 })
 .catch(() => {
   this.$Progress.fail();
 })
},

    editModal(user){
      this.editmode = true;
    this.form.reset();
$('#addNew').modal('show');
this.form.fill(user);
  },
  newModal(){
      this.editmode = false;
    this.form.reset();
$('#addNew').modal('show');
  },
deleteUser(id){
swal.fire({
  title: 'Are you sure you want to delete?',
  text: "You won't be able to revert this!",
  type: 'warning',
  showCancelButton: true, 
  confirmButtonColor: '#3085d6',
  cancelButtonColor: '#d33',
  confirmButtonText: 'Yes, delete it!'
}).then((result) => {
  if (result.value) {
this.form.delete('api/user/'+id).then(()=>{


    swal.fire(
      'Deleted!',
      'Your file has been deleted.',
      'success'
    )
 
 Fire.$emit('AfterCreate');

}).catch(()=>{
  swal("Failed!", "There was something wrong", "warning");
})
 }
})
},

 loadUsers(){
               
 axios.get("api/user").then((data) => {this.users = data});
          
            },
    createUser(){
        this.$Progress.start();
        this.form.post('api/user')
        .then(()=>{
                Fire.$emit('AfterCreate');
        $('#addNew').modal('hide');
Toast.fire({
  type: 'success',
  title: 'User successfully created'
})

                this.$Progress.finish();
        })
        .catch(()=>{

        })
  
    }
},
        created() {
            this.loadUsers();
            Fire.$on('AfterCreate', () => {
              this.loadUsers();
            });
        //  setInterval(() => this.loadUsers(), 3000);
      
        }
    }
</script>
