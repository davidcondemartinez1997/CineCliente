<template>
 <div id="form">
  <form v-if="seen" class="form-horizontal" onsubmit="return false">
   <h1 class="col-sm-12">Formulario Entrada</h1>
   <div class="form-group">
    <label  class="control-label col-sm-2">Pelicula:</label>
    <input type="text" id="pelicula" name="pelicula" required="required" class="form-control" v-bind:value="entrada.Pelicula"/>   
  </div>
  <div class="form-group">
    <label class="control-label col-sm-2">Precio:</label>
    <input type="text" id="precio" name="precio" class="form-control" required="required" v-bind:value="entrada.Precio"/>   
  </div>
  <div class="form-group">
    <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
    <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="entrada.Id !== -1">Eliminar</button>
  </div>


</form>
</div>
</template>

<script>
 import axios from 'axios';
 import {EventBus} from './EventBus.js';
 let url = config.address + 'Entrada/';
 export default {
  name: 'app',
  data () {
   return {
    entrada: {},
    seen: true,
  }
},
methods: {
 enviar: function(){
  let data = {
   Pelicula: document.getElementById("pelicula").value,
   Precio: document.getElementById("precio").value,
   Id: this.entrada.Id
 }
 if(data.Pelicula !== "" && data.Precio !== "" ){
   if(data.Id  == -1){
     axios.post(url ,data)
     .then(response => {
      this.entrada.Id = response.data.Id;
      this.entrada.Pelicula = response.data.Pelicula;
      this.entrada.Precio = response.data.Precio;
      console.log(this.entrada);
      this.fireEvent();
    })
     .catch(response => {
      swal(
        '',
        'Ha ocurrido un error',
        'error'
        )
    })
   }
   else{
    if(data.Pelicula !== this.entrada.Pelicula && data.Precio !== this.entrada.Precio){

     axios.put(url + data.Id, data)
     .then(response => {
      this.fireEvent();
    })
     .catch(response => {
      swal(
        '',
        'Ha ocurrido un error',
        'error'
        )
    })
   }else{
    swal(
        '',
        'Los datos no han cambiado',
        'info'
        )
   }
 }
}


},
fireEvent: function(){
  swal(
    '',
    'Operacion completada con exito!',
    'success'
    )
  EventBus.$emit("updateEntrada", this.entrada);
},
eliminar: function(){
  if(this.entrada.Id != -1){
   this.seen = false;
   axios.delete(url + this.entrada.Id)
   .then(response => {
    this.fireEvent();
  })
   .catch(response => {
    swal(
      '',
      'Ha ocurrido un error',
      'error'
      )
  })
 }

}
},
created() {
 this.entrada = this.$parent.entrada;
}

}
</script>

<style>
</style>
