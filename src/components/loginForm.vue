<template>
  <div>
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
                      <label for="Email">Email address</label>
                      <input v-model="correo" name="Email1" id="Email" type="email" class="form-control" placeholder="Correo" required>  
                    </div>
                    <div class="form-group">
                      <label for="Password1">Password</label>
                      <input  v-model="clave" name="Password1" id="Password1" type="password" class="form-control" placeholder="Password" required>
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
    <div class="container-fluid my-3">
      <br>
      <div class="row">
        <div class="col-12 col-md-8 col-lg-6 col-xl-4 mx-auto">
          <div class="card">
            <div class="card">
              <div class="card-header"><h3>Registrarse en el Sistema</h3></div>
                <div class="card-body">
                  <form>
                    <div class="form-group">
                      <label for="email">Email address</label>
                      <input type="text" v-model="email" name="email" id="email" class="form-control" placeholder="Correo">
                    </div>
                    <div class="form-group">
                      <label for="pass">Password</label>
                      <input type="password" v-model="pass" name="pass" id="pass" class="form-control" placeholder="Password">
                    </div>
                    <div class="form-group">
                      <button type="submit" v-on:click="registrar" class="btn btn-primary">Registrar</button>  
                    </div>
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
            clave:'',
            email:'',
            pass:''
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
               });
         },
         registrar:function(e) {
           e.preventDefault();
            if(this.email.length === 0 || this.pass.length === 0)
              alert("El correo o la contraseña no pueden ser vacios");
            else{
              this.firebase.auth().createUserWithEmailAndPassword(this.email,this.pass)
                .then((res)=>{
                  this.firebase.auth().currentUser.sendEmailVerification();
                  alert("Se envio un correo a la direccion ingresada por favor revisala");
                  console.log(res);
                  location.reload();  

                })
                .caller((error)=>{
                  alert("Algo salio mal, vuelve a intentarlo",error);
                  location.reload();
                })
              }
            
        
         }
      }
   }
</script>
