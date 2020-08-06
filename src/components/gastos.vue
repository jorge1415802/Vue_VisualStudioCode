<template>
   <div id="gasto">
      <div class="container">
         <div class="row">
            <div class="col-12">
               <h3 class="text-primary text-center">Lista de Gastos</h3>
               <hr>
            </div>
            <div class="col-8 mx-auto">
               <div class="card shadow">
                  <div class="card-body">
                     <form @submit="submit" method="post">
                        <div class="form-group row">
                           <label for="inputEmail3" class="col-sm-4 col-form-label text-right">Nombre del Gasto:</label>
                           <div class="col-sm-8">
                              <input type="text" class="form-control" v-model="Nombre" required>
                           </div>
                        </div>
                        <div class="form-group row">
                           <label for="inputEmail3" class="col-sm-4 col-form-label text-right">Monto del Gasto:</label>
                           <div class="col-sm-8">
                              <input type="text" class="form-control" v-model="Monto" required>
                           </div>
                        </div>
                        <div class="form-group row">
                           <label for="inputEmail3" class="col-sm-4 col-form-label text-right">Tipo de Gasto:</label>
                           <div class="col-sm-8">
                              <input type="text" class="form-control" v-model="Tipo" required>
                           </div>
                        </div>
                        <div v-if="cargar"  class="d-flex justify-content-center">
                           <div class="spinner-border text-center" role="status">
                              <span class="sr-only">Loading...</span>
                           </div>
                        </div>
                        <div v-else class="text-center">
                           <button type="submit" class="btn btn-primary">Agregar</button>
                        </div>
                        
                     </form>  
                  </div>
               </div>
            </div>
            <div class="col-12">
            <hr>
               <div class="btn-group" role="group">
                  <div v-for="Filtro in Filtros" v-bind:key="Filtro">
                    <button type="button" class="btn btn-outline-success" v-on:click="Filtrar(Filtro)" >{{Filtro}}</button>
                  </div>
                  <button type="button" class="btn btn-outline-danger mx-2" v-on:click="SinFiltro()" >x</button>
               </div>
            <hr>
            </div>
            <div class="col-12">
               <div class="card shadow">
                  <div class="card-body">
                     <table class="table table-sm table-hover table-bordered table-striped">
                        <thead>
                           <tr class="bg-primary">
                              <th>#</th>
                              <th>Nombre del Gasto</th>
                              <th>Monto del Gasto</th>
                              <th>Tipo de Gastos</th>
                              <th>Acción</th>
                           </tr>
                        </thead>
                        <tbody>
                           <tr v-for="(item, index) in ListaGatos" v-bind:key="item.id">
                              <td>{{index+1}}</td>
                              <td>{{item.Nombre}}</td>
                              <td>{{item.Monto}}</td>
                              <td>{{item.Tipo}}</td>
                              <td> 
                                 <button title="Editar" type="button"  v-on:click="Consultar(item.id)" class="btn btn-outline-warning btn-sm mr-1"><i class="fa fa-pencil" aria-hidden="true"></i></button> 
                                 <button title="Eliminar" type="button"  v-on:click="Eliminar(item)" class="btn btn-outline-danger btn-sm"><i class="fa fa-trash" aria-hidden="true"></i></button> 
                              </td>                          
                           </tr>                           
                        </tbody>
                        <tfoot>
                           <tr>
                              <td colspan="2"> Total</td>
                              <td>{{Total}}</td>
                              <td colspan="2"> </td>

                           </tr>
                        </tfoot>  
                     </table>
                  </div>
               </div>
            </div>
         </div>
      </div>
   </div>
</template>
<script>
export default {
   name: "gastos",
   data: function() {
      return {
         coleccionGastos:'gastos',
         id:'',
         Nombre:'',
         Monto:'',
         Tipo:'',
         ListaFiltros:{},
         ListaGatos:{},
         Total:0,
         Filtros:[],
         cargar:false
      }
   },
   props: ["firebase","idUsuario","db"],
   methods:{
      Consulta: function(){
         
         let gatos = this.db.collection(this.coleccionGastos);
         gatos.get()
         .then(resp => {
            if (resp.empty) {
               console.log('No matching documents.');
               return;
            }
            let Lista = [];
            let Total = 0.00;
            let Filtros = [];
            resp.forEach(doc => {
               let data = {
                  id:doc.id,
                  Monto:doc.data().Monto,
                  Nombre:doc.data().Nombre,
                  Tipo:doc.data().Tipo,
               }     
               Total += parseFloat(doc.data().Monto);       
               Lista.push(data);
               /* Añadir Filtros */
               let consul = Filtros.find(element => element === doc.data().Tipo);
               console.log(consul)
               if(consul==undefined){
                  Filtros.push(doc.data().Tipo);
               }
            }); 
            this.Total=Total;
            this.ListaGatos = Lista;
            this.ListaFiltros = Lista;

            this.Filtros = Filtros;

         })
         .catch(err => {
            console.log('Error getting documents', err);
         })
  
      },
      Consultar: function(id){
         this.db.collection(this.coleccionGastos).doc(id).get().then(resp => {
            this.id = resp.id;
            this.Nombre= resp.data().Nombre;
            this.Monto= resp.data().Monto;
            this.Tipo= resp.data().Tipo;
         });
      },
      Eliminar: function(data){
         var r = confirm(`Desea Eliminar el Gasto ${data.Nombre}` );
         if (r == true) {
          this.db.collection(this.coleccionGastos).doc(data.id).delete().then(resp => {
             console.log(resp);
             this.Consulta();
          });
         } 
         
      },
      limpiar: function(){
         this.id='';
         this.Nombre='';
         this.Monto='';
         this.Tipo='';
      },
      SinFiltro: function(){
         this.ListaGatos = this.ListaFiltros;
         this.Calcular();
      },
      Calcular: function(){
         let datos = this.ListaGatos;
         let Total = 0;
         datos.forEach(element => {
            Total += parseFloat(element.Monto);  
         });
         this.Total=Total;
      },
      Filtrar: function(Tipo){
         let Datos = this.ListaFiltros;
         let Filtrado = Datos.filter(Dato => Dato.Tipo ==Tipo );
         this.ListaGatos = Filtrado;
         this.Calcular();
      },
      submit: function(e){
         
         e.preventDefault();
         this.cargar = true;
         let data ={
            Monto: this.Monto,
            Nombre: this.Nombre,
            Tipo:this.Tipo
         }
         if(this.id !=''){
            this.db.collection(this.coleccionGastos).doc(this.id).set(data).then(ref => {               
                  this.Consulta();
                  this.limpiar();
                  this.cargar = false;
                  console.log(ref);
                  
            });
         }else{
            this.db.collection(this.coleccionGastos).add(data).then(ref => {               
                  this.Consulta();
                  this.limpiar();
                  this.cargar = false;
                  console.log(ref);
                  
            });
         }
      }
   },   
   beforeMount: function(){
      //consultar la lista
      this.Consulta();
   },
}
</script>