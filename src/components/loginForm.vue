<template>
    <div id="login">
      <div class="container-fluid my-3">
        <div class="row">
          <div class="col-12 col-md-8 col-lg-6 col-xl-4 mx-auto">
            <div class="card">
              <div class="card">
                <div class="card-header">
                  <h3>Ingreso al Sistema</h3>
                </div>
                <div class="card-body">
                  <form @submit="ingresar">
                    <div class="form-group">
                      <label for="exampleInputEmail1">Email address</label>
                      <input v-model="correo" type="email" class="form-control " required>  
                    </div>
                    <div class="form-group">
                      <label for="exampleInputPassword1">Password</label>
                      <input  v-model="clave"  type="password" class="form-control" required>
                    </div>
                    <button type="submit" class="btn btn-primary">Ingresar</button>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
   export default {
      name: "loginForm",
      data: function() {
         return {
            correo:'',
            clave:''
         }
      },
      props: ["firebase"],
      methods: {
         ingresar: function(e) {
           e.preventDefault();
           
            this.firebase.auth().signInWithEmailAndPassword(this.correo, this.clave)
               .then((response) => {
                  this.$emit("ingresoCorrecto", response.user.email);
               })
               .catch((error) => {
                  let errorCode = error.code;
                  let errorMessage = error.message;
                  alert("Correo invalido ",errorCode,errorMessage);
               })
         }
      }
   }
</script>
