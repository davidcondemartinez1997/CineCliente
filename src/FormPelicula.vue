<template>
  <div id="form">
    <form v-if="seen" class="form-horizontal" onsubmit="return false" >

      <h1>Formulario Pelicula <a class="close" v-on:click="close">&times;</a></h1>
      <div class="form-group">
        <label class="control-label">Titulo:</label>
        <input type="text" id="titulo" name="titulo" class="form-control" required="required" v-bind:value="pelicula.Titulo"/>   
      </div>
      <div class="form-group">
        <label class="control-label col-sm-2">Director:</label>
        <input type="text" id="director" name="director" class="form-control" required="required"  v-bind:value="pelicula.Director"/>   
      </div>

      <div class="form-group">
        <label class="control-label col-sm-2">Pais:</label>
        <input type="text" id="pais" name="pais" class="form-control" required="required"  v-bind:value="pelicula.Pais" />   
      </div>

      <div class="form-group">
        <label class="control-label col-sm-2">Genero:</label>
        <input type="text" id="genero" name="genero" class="form-control" required="required"  v-bind:value="pelicula.Genero" />   
      </div>
      <div class="form-group">
        <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
        <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="pelicula.Id !== -1">Eliminar</button>
      </div>

    </form>
  </div>
</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  let url = config.address + 'Pelicula/';
  export default {
    name: 'app',
    data () {
      return {
       pelicula: {},
       seen: true
     }
   },
   methods: {
    enviar: function(){
      let preventUpdate = false;
      let data = {
        Titulo: document.getElementById("titulo").value,
        Director: document.getElementById("director").value,
        Pais: document.getElementById("pais").value,
        Genero: document.getElementById("genero").value,
        Id: this.pelicula.Id
      }
      if(data.Titulo !== "" && data.Director !== "" && data.Pais !== "" && data.Genero !== ""){
        if(data.Id == -1){
          axios.post(url, data)
          .then(response => {
            this.pelicula.Id = response.data.Id;

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
          if(data.Titulo !== this.pelicula.Titulo && data.Director !== this.pelicula.Director  && data.Pais !== this.pelicula.Pais && data.Genero !== this.pelicula.Genero){
            if(data !== this.pelicula){
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
            }
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
      EventBus.$emit("updatePelicula", this.pelicula);
    },

    eliminar: function(){
      if(this.pelicula.Id != -1){
        this.seen = false;
        axios.delete(url + this.pelicula.Id)
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
    },

    close: function(){
      document.getElementById("form").innerHTML = "";
    }
  },
  created() {
    this.seen = true;
    this.pelicula = this.$parent.pelicula;
  }

}
</script>

<style>
</style>
