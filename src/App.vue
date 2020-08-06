<template>
  <div id="app">
    <!-- Sin Logeo -->
    <div id='login' v-if='!logon' >
      <loginForm v-bind:firebase="firebase" 
                 v-on:ingresoCorrecto='ingresoCorrecto($event)'></loginForm>
    </div>
    <!-- ya logeado -->
    <div id='inicio' v-else>
      <gastos v-bind:firebase="firebase" v-bind:db="db"></gastos>
    </div>
  </div> 
</template>

<script>

import firebase from 'firebase';
import 'firebase/firestore';
import 'firebase/auth';
import loginForm from './components/loginForm.vue';
import gastos from './components/gastos.vue';


export default {
  name: 'app',
  data: function (){
    return {
      logon:false,
      coleccionRaiz:'usuarios',
      idUsuario:'null',
      userData:{},     
      firebase:'',
      db:''
    }
  },
  components: {
    loginForm,
    gastos
  },
  beforeMount: function () {
    var config = {
      apiKey: "AIzaSyDKlxHIGENnIeuGFyoAJvtYrCttEKN5DBU",
      authDomain: "gastos-4a37b.firebaseapp.com",
      databaseURL: "https://gastos-4a37b.firebaseio.com",
      projectId: "gastos-4a37b",
      storageBucket: "gastos-4a37b.appspot.com",
      messagingSenderId: "518594220357",
      appId: "1:518594220357:web:28b940b7f1bff4024e6e2c",
      measurementId: "G-N3JSF9S4FG"
    };
    firebase.initializeApp(config);
    this.db = firebase.firestore();
    const settings = {timestampsInSnapshots: true};
    this.db.settings(settings);
    this.firebase=firebase;
  },
  methods: {
    ingresoCorrecto: function(usuario) {
      this.idUsuario=usuario;
      this.logon=true;
    }
  }
}
</script>
