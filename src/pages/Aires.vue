<template>
  <div>
    <div class="page-header page-header-small">
      <parallax
        class="page-header-image"
        style="background-image: url('img/airesLanding.jpg')"
      >
      </parallax>
      <div class="content-center">
        <div class="container">
          <h1 class="title">Aires acondicionados</h1>
        </div>
      </div>
    </div>
    <div class="section section-form text-center" id="updateForm">
      <div class="container">
        <h2 class="title">Aqui también puedes probar la API</h2>
        <p class="description" v-if="updating">Actualizar proveedor</p>
        <p class="description" v-else>Registrar proveedor</p>
        <div class="row">
          <div class="col-lg-6 text-center ml-auto mr-auto col-md-8">
            <fg-input
              class="input-lg"
              placeholder="Nombre..."
              v-model="form.name"
              addon-left-icon="now-ui-icons users_circle-08"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Direccion..."
              v-model="form.direccion"
              addon-left-icon="now-ui-icons location_pin"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Telefono..."
              v-model="form.telefono"
              addon-left-icon="now-ui-icons tech_mobile"
            >
            </fg-input>
            <div class="send-button">
              <div class="row d-flex justify-content-center" v-if="updating">
                <n-button type="primary" round size="lg" @click="updateSupplier(form)">Actualizar</n-button>
                <n-button class="mx-4" type="secondary" round size="md" @click="cancelUpdating()">Cancelar</n-button>
              </div>
              <n-button type="primary" round block size="lg" @click="registerSupplier(form)" v-else>Registrar</n-button>
            </div>
            <span>{{ message }}</span>
          </div>
        </div>
      </div>
    </div>
    <div class="section text-center" v-if="renderComponent">
      <div class="container">
        <h2 class="title" v-if="suppliers.length > 0">Proveedores registrados</h2>
        <h4 class="title mt-0 pt-0" v-else>No hay proveedores registrados</h4>
        <div v-for="supplier in suppliers" :key="supplier.id" class="card mx-2" style="width: 20rem;" title="Editar" @click="toUpdateSupplier(supplier.id, {behavior: 'smooth'})">
          <div class="h2 mr-2 deleteSupplier" title="Eliminar" @click="deleteSupplier(supplier.id)">
            &times;
          </div>
          <div class="card-body">
            <h4 class="card-title mt-0">{{ supplier.name }}</h4>
            <p class="card-text">{{ supplier.direccion }}</p>
            <p class="card-text">{{ supplier.telefono }}</p>
          </div>
        </div>
      </div>
    </div>
    <alert type="success" v-if="successMessage != ''">
      <div class="alert-icon">
        <i class="now-ui-icons ui-2_like"></i>
      </div>
      <strong>{{ successMessage }}</strong>  
    </alert>
    <alert type="danger" v-if="errorMessage != ''">
      <div class="alert-icon">
        <i class="now-ui-icons travel_info"></i>
      </div>
      <strong>{{ errorMessage }}</strong>  
    </alert>
  </div>
</template>
<script>
import { Alert, Button, FormGroupInput } from '@/components';
import axios from "axios";

export default {
  name: 'landing',
  bodyClass: 'landing-page',
  components: {
    [Button.name]: Button,
    [FormGroupInput.name]: FormGroupInput,
    Alert
  },
  data() {
    return {
      form: {
        name: "",
        direccion: "",
        telefono: "",
      },
      message : "",
      suppliers : [],
      renderComponent: true,
      successMessage : "",
      errorMessage : "",
      updating: false,
      supplierToUpdate: null,
    };
  },
  mounted() {
    this.getSuppliers();
  },
  methods: {
    registerSupplier: function(supplier){
       if(supplier.name.trim() != ""){
        axios.post(this.$apiURL+"/supplier", supplier)
        .then((response) => {
          console.log(response)
          if(response.data){
            this.successMessage = "Proveedor registrado!"
          }else{
              this.errorMessage = "Error registrando proveedor"
            }
        }).then(()=>{
          this.getSuppliers()
          this.forceRerender()
        })
       }else{
         this.errorMessage = "Ingrese el nombre del proveedor"
       }
       this.restartFields()
    },
    getSuppliers: function(){
      axios.get(this.$apiURL+"/suppliers")
        .then((response) => {
          if(response.data){
            this.suppliers = response.data
          }else{
            this.suppliers = []
          }
        })
    },
    toUpdateSupplier(supplierId, options){
      this.supplierToUpdate = supplierId
      document.getElementById("updateForm").scrollIntoView(options);
      axios.get(this.$apiURL + "/supplier/" + supplierId)
        .then((response) => {
          this.form.name = response.data.name
          this.form.direccion = response.data.direccion
          this.form.telefono = response.data.telefono
          this.updating = true
        })
      
    },
    updateSupplier(supplier){
      if(supplier.name.trim() != ""){
        axios.put(this.$apiURL + "/supplier/" + this.supplierToUpdate, supplier)
          .then((response) => {
            if(response.data){
              this.successMessage = "Proveedor actualizado!"
            }else{
              this.errorMessage = "Error actualizando proveedor"
            }
            this.updating = false
          }).then(()=>{
            this.getSuppliers()
            this.forceRerender()
          })
      }else{
        this.errorMessage = "Ingrese el nombre del proveedor"
      }
      this.restartFields()
    },
    cancelUpdating(){
      this.restartFields()
      this.updating = false
    },
    deleteSupplier: function(supplierId){
      axios.delete(this.$apiURL + "/supplier/" + supplierId)
        .then((response) => {
          console.log(response.data)
          this.getSuppliers()
          this.forceRerender()
          this.successMessage = "Proveedor eliminado!"
        }).catch(()=>{
          this.errorMessage = "Error eliminando proveedor"
        })
      this.hideNotifications()
    },
    forceRerender() {
        this.renderComponent = false;
        this.$nextTick(() => {
          this.renderComponent = true;
        });
    },
    hideNotifications(){
      setTimeout(() => {
        this.successMessage = ""
        this.errorMessage = ""
      }, 4000);
    },
    restartFields(){
      this.form.name = ""
      this.form.direccion = ""
      this.form.telefono = ""
      this.hideNotifications()
    }

  }
};
</script>

<style scoped>
.deleteSupplier{
  float: right;
  transition: transform .2s;
}

.deleteSupplier:hover{
  cursor: pointer;
  transform: scale(1.2);
}
div.alert{
  position: fixed;
  bottom: 0;
  right: 0;
  z-index: 1;

}

div.card{
  transition: transform .2s;
}

div.card:hover{
  cursor: pointer;
  transform: scale(1.03);
}
</style>
