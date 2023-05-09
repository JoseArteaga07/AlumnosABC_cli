<template>
  <div>
    <div class="container p-3 my-3 border">

      <div class="container">
        <div class="row">
          <div class="col-sm-10">
            <h3>Alumnos</h3>
          </div>
          <div class="col-sm">
            <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#Registro"
              @click="modalTitle = 'Registrar'">Registrar</button>
          </div>
        </div>
      </div>

    </div>

    <div class="container p-3 my-3 border">

      <div v-for="item in dataApi">

        <div class="card text-center">
          <div class="card-header">

          </div>
          <div class="card-body">

            <div class="container">
              <div class="row">
                <div class="col-sm-3">
                  <h5 class="card-title">Nombre</h5>
                  <p class="card-title" style="font-size: 18px;">{{ item.Nombre }}</p>
                </div>
                <div class="col-sm-2">
                  <h5 class="card-title">Universidad</h5>
                  <p class="card-title" style="font-size: 18px;">{{ item.Universidad }}</p>
                </div>
                <div class="col-sm-2">
                  <h5 class="card-title">Promedio</h5>
                  <p class="card-title" style="font-size: 18px;">{{ item.Promedio }} / 100.00</p>
                </div>
                <div class="col-sm-3">
                  <br>
                  <button type="button" class="btn btn-danger" @click="deletebyId(item.id);">Eliminar</button>
                </div>
                <div class="col-sm-2">
                  <br>
                  <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#Registro"
                    @click="dataForAxios = item; modalTitle = 'Editar'">Editar</button>
                </div>

              </div>
            </div>

            <p class="card-text"></p>

          </div>
          <div class="card-footer text-muted">

          </div>
        </div>
        <br>

      </div>

    </div>

    <div>

      <div class="modal fade" id="Registro" tabindex="-1" role="dialog" aria-labelledby="RegistroCenterTitle"
        aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="RegistroLongTitle">{{ modalTitle }}</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">

              <div class="form-group row">
                <label for="name" class="col-sm-2 col-form-label">Nombre:</label>
                <div class="col-sm-10">
                  <input type="text" class="form-control" id="name" maxlength="100" placeholder="Nombre"
                    autocomplete="off" v-model="dataForAxios.Nombre">
                </div>
              </div>

              <div class="form-group row">
                <label for="promedio" class="col-sm-2 col-form-label">Promedio:</label>
                <div class="col-sm-10">
                  <input type="number" min="0" max="100" class="form-control" id="promedio" placeholder="Promedio"
                    autocomplete="off" @change="val();" v-model="dataForAxios.Promedio">
                </div>
              </div>

              <div class="form-group row">
                <label for="promedio" class="col-sm-3 col-form-label">Universidad:</label>
                <select v-model="dataForAxios.Universidad">
                  <option v-for="option in options" v-bind:value="option.value">
                    {{ option.text }}
                  </option>
                </select>
              </div>

            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal" @click="dataForAxios={ Universidad: 'Tec Laguna',};">Cerrar</button>
              <button type="button" class="btn btn-primary" data-dismiss="modal" @click="registro();">{{ modalTitle
              }}</button>
            </div>

          </div>
        </div>
      </div>


    </div>

  </div>
</template>

<script>
import axios from "axios";
import urlApi from "../router/apiurl";
export default {
  name: 'HelloWorld',
  data() {
    return {
      modalTitle: '',
      dataApi: [],
      dataForAxios: {
        Nombre: '',
        Promedio: 0,
        Universidad: 'Tec Laguna',
      },
      options: [
        { text: 'Tec Laguna', value: 'Tec Laguna' },
        { text: 'UAC', value: 'UAC' },
        { text: 'UVM', value: 'UVM' },
        { text: 'Tecmilenio', value: 'Tecmilenio' },
      ],


    }
  },
  created() {
    this.loadPage();
  },
  methods: {
    async loadPage() {
      axios.get(urlApi.urlApi + '/api/GetData').then((response) => {
        //console.log(response.data);
        this.dataApi = response.data.data;
      });
    },
    async registro() {

      if (this.modalTitle == 'Registrar') {

        this.modalTitle = '';
        axios.post(urlApi.urlApi + '/api/Register', {
          name: this.dataForAxios.Nombre,
          promedio: this.dataForAxios.Promedio,
          universidad: this.dataForAxios.Universidad
        }).then((response) => {
          //console.log(response.data);
          this.dataForAxios = { Universidad: 'Tec Laguna',};
          if (response.data.message = 'Success') {
            Swal.fire('Registro Exitoso');
            this.loadPage();
          }
          else {
            Swal.fire('Error');
            this.loadPage();
          }
        });

      }
      else {
        //console.log(this.dataForAxios)
        this.modalTitle = '';
        axios.put(urlApi.urlApi + '/api/Update/' + this.dataForAxios.id, {
          name: this.dataForAxios.Nombre,
          promedio: this.dataForAxios.Promedio,
          universidad: this.dataForAxios.Universidad
        }).then((response) => {
          //console.log(response.data);
          this.dataForAxios = { Universidad: 'Tec Laguna',};
          if (response.data.message = 'Success') {
            Swal.fire('Cambio Exitoso');
            this.loadPage();
          }
          else {
            Swal.fire('Error');
            this.loadPage();
          }
        });

      }
    },
    deletebyId(id){
     
      axios.delete(urlApi.urlApi + '/api/Delete/' + id)
      .then((response) => {
          if (response.data.message = 'Success') {
            Swal.fire('Registro Eliminado');
            this.loadPage();
          }
          else {
            Swal.fire('Error');
            this.loadPage();
          }
        });

    },
    val() {
      //console.log('entra')
      this.name = this.name.length > 0 && this.name.length <= 100 ? this.name : '';
      this.promedio = this.promedio >= 0 && this.promedio <= 100 ? this.promedio : 0;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
