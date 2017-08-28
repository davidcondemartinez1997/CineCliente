<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Entradas</h1>
      <table v-if="entradas && entradas.length" class="table table-bordered">
        <tr>
          <th>Pelicula</th>
          <th>Precio</th>
        </tr>
        <tr v-for="entradas of entradas" v-on:click="detail" v-bind:id="entradas.Id">
          <td>{{entradas.Pelicula}}</td>
          <td> {{entradas.Precio}}</td>
        </tr>
      </table>

      <button id="submit" class="btn btn-success btn-block" v-on:click="nuevo">Nuevo</button>
    </div>
    <div id="form" ></div>
  </div>

</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  import Form from './FormEntrada.vue'
  import Vue from 'vue'

  export default {
    name: 'app',
    data () {
      return {
        entradas: undefined
      }
    },
    methods: {
      detail: function (e) {
        let id = e.target.id;
        if(id == ""){
          id = e.target.parentNode.id;
        }
        this.entradas.forEach((p, index) => {
          if(p.Id == id){
            new Vue({
              el: '#form',
              render: h => h(Form),
              data: {
                entrada: p
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
            entrada: {
              Pelicula: undefined,
              Precio: undefined,
              Id:-1
            }
          },
        });

      },
      init: function(){
        let url = config.address + 'Entrada/';
        axios.get(url)
        .then(response => {
          this.entradas = response.data;
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
    created() {
      this.init();
      EventBus.$on('updateEntrada', (() => {
        this.init();
      }));


    }
  }
</script>

<style>
</style>
