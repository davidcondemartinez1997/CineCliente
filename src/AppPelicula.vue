<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Peliculas</h1>
      <table v-if="peliculas && peliculas.length" class="table table-bordered">
        <tr>
          <th>Titulo</th>
          <th>Director</th>
          <th>Pais</th>
          <th>Genero</th>
        </tr>
        <tr v-for="pelicula of peliculas" v-on:click="detail" v-bind:id="pelicula.Id">
          <td>{{pelicula.Titulo}}</td>
          <td> {{pelicula.Director}}</td>
          <td> {{pelicula.Pais}}</td>
          <td> {{pelicula.Genero}}</td>
        </tr>
      </table>
      <button id="submit" class="btn btn-success btn-block" v-on:click="nuevo">Nuevo</button>
    </div>
    <div id="form" ></div>
  </div>

</template>

<script>
  import axios from 'axios';
  import Form from './FormPelicula.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

  export default {
    name: 'app',
    data () {
      return {
        peliculas: undefined,
      }
    },
    methods: {
      detail: function (e) {
        let id = e.target.id;
        if(id == ""){
          id = e.target.parentNode.id;
        }
        this.peliculas.forEach((p, index) => {
          if(p.Id == id){
            new Vue({
              el: '#form',
              render: h => h(Form),
              data: {
                pelicula: p
              },
            });
          }
        });
      },
      nuevo: function (e) {
        new Vue({
          el: '#form',
          render: h => h(Form),
          data: {
            pelicula: {
              Titulo: undefined,
              Director: undefined,
              Pais: undefined,
              Genero: undefined,
              Id: -1
            }
          },
        });
      },
      init: function(){
        let url = config.address + 'Pelicula/';
        axios.get(url)
        .then(response => {
          this.peliculas = response.data;
        })
      }

    },
    created() {
      this.init();
      EventBus.$on('updatePelicula', () => {
        this.init();
      });

    }

  }
</script>

<style>
</style>
