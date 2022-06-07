<template>
  <div>
    <div class="page-header page-header-small">
      <parallax
        class="page-header-image"
        style="background-image: url('img/pruebasLanding.jpg')"
      >
      </parallax>
      <div class="content-center">
        <div class="container">
          <h1 class="title">Pruebas</h1>
        </div>
      </div>
    </div>
    <div class="section section-form text-center">
      <div class="container">
        <h2 class="title">Aqui puedes probar la API</h2>
        <p class="description">Registrar usuario</p>
        <div class="row">
          <div class="col-lg-6 text-center ml-auto mr-auto col-md-8">
            <fg-input
              class="input-lg"
              placeholder="Nombre..."
              v-model="userForm.nombre"
              addon-left-icon="now-ui-icons users_circle-08"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Email..."
              v-model="userForm.email"
              type="email"
              addon-left-icon="now-ui-icons ui-1_email-85"
              @change="validateEmail"
            >
            </fg-input>
            <div class="send-button">
              <n-button type="primary" round block size="lg" @click="registerUser()"
                >Registrar</n-button
              >
            </div>
            <span>{{ message }}</span>
          </div>
        </div>
      </div>
    </div>
    <div class="section text-center" v-if="renderComponent">
      <div class="container">
        <h2 class="title" v-if="users.length > 0">Usuarios registrados</h2>        
        <h4 class="title mt-0 pt-0" v-else>No hay usuarios registrados</h4>
        <div v-for="user in users" :key="user.id" class="card mx-2" style="width: 20rem;">
          <div class="h2 mr-2 deleteUser" title="Eliminar" @click="deleteUser(user.id)">
            &times;
          </div>
          <div class="card-body">
            <h4 class="card-title mt-0">{{ user.nombre }}</h4>
            <p class="card-text">{{ user.email }}</p>
          </div>
        </div>
      </div>
    </div>
    <hr>
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
              v-model="supplierForm.name"
              addon-left-icon="now-ui-icons users_circle-08"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Direccion..."
              v-model="supplierForm.direccion"
              addon-left-icon="now-ui-icons location_pin"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Telefono..."
              v-model="supplierForm.telefono"
              addon-left-icon="now-ui-icons tech_mobile"
            >
            </fg-input>
            <div class="send-button">
              <div class="row d-flex justify-content-center" v-if="updating">
                <n-button type="primary" round size="lg" @click="updateSupplier(supplierForm)">Actualizar</n-button>
                <n-button class="mx-4" type="secondary" round size="md" @click="cancelUpdating()">Cancelar</n-button>
              </div>
              <n-button type="primary" round block size="lg" @click="registerSupplier(supplierForm)" v-else>Registrar</n-button>
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
        <div v-for="supplier in suppliers" :key="supplier.id" class="card mx-2" style="width: 20rem;">
          <div class="h2 mr-2 deleteSupplier" title="Eliminar" @click="deleteSupplier(supplier.id)">
            &times;
          </div>
          <div class="card-body" title="Editar" @click="toUpdateSupplier(supplier.id, {behavior: 'smooth'})">
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
import { Alert, Button, FormGroupInput, Card } from "@/components";
import axios from "axios";

export default {
  name: "pruebas",
  bodyClass: "landing-page",
  components: {
    [Button.name]: Button,
    [FormGroupInput.name]: FormGroupInput,
    [Card.name]: Card,
    [Alert.name]: Alert,
  },
  data() {
    return {
      userForm: {
        nombre: "",
        email: "",
      },
      message : "",
      validEmail: false,
      users : [],
      renderComponent: true,
      supplierForm:{
        name: "",
        direccion: "",
        telefono: "",
      },
      suppliers : [],
      successMessage : "",
      errorMessage : "",
      updating: false,
      supplierToUpdate: null,
    };
  },
  mounted() {
    this.getUsers();
    this.getSuppliers();
  },
  methods: {
    registerUser: function(){
      //  axios.post(this.$apiURL+"/user", {"nombre": this.userForm.name,"email": this.userForm.email})
       if(this.userForm.nombre.trim() != "" && this.validEmail){
        axios.post(this.$apiURL+"/user", this.userForm)
        .then((response) => {
          // console.log(response)
          if(response.data){
            this.message = "Usuario registrado!"
          }else{
            this.message = this.userForm.email + " ya está registrado"
          }
        }).then(()=>{
          this.getUsers()
          this.forceRerender()
        })
       }else{
         this.message = "ERROR"
       }
    },
    validateEmail() {      
      const emailPattern = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      if (emailPattern.test(this.userForm.email)) {
          this.message = '';
          this.validEmail = true;
      } else {
          this.message = 'Email invalido!';
          this.validEmail = false;
      }
    },
    getUsers: function(){
      axios.get(this.$apiURL+"/users")
        .then((response) => {
          if(response.data){
            this.users = response.data
            // console.log("ALL USERS")
            // console.log(this.users)
          }else{
            this.users = []
          }
        })
    },
    deleteUser: function(userId){
      axios.delete(this.$apiURL + "/user/" + userId)
        .then((response) => {
          // console.log(response.data)
          this.getUsers()
          this.forceRerender()
        })
    },
    registerSupplier: function(supplier){
       if(supplier.name.trim() != ""){
        axios.post(this.$apiURL+"/supplier", supplier)
        .then((response) => {
          // console.log(response)
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
            this.supplierForm.name = response.data.name
            this.supplierForm.direccion = response.data.direccion
            this.supplierForm.telefono = response.data.telefono
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
          // console.log(response.data)
          this.getSuppliers()
          this.forceRerender()
          this.successMessage = "Proveedor eliminado!"
        }).catch(()=>{
          this.errorMessage = "Error eliminando proveedor"
        })
      this.hideNotifications()
    },
    hideNotifications(){
      setTimeout(() => {
        this.successMessage = ""
        this.errorMessage = ""
      }, 4000);
    },
    restartFields(){
      this.supplierForm.name = ""
      this.supplierForm.direccion = ""
      this.supplierForm.telefono = ""
      this.hideNotifications()
    },
    forceRerender() {
        this.renderComponent = false;
        this.$nextTick(() => {
          this.renderComponent = true;
        });
    }
  }
};
</script>

<style scoped>

.deleteUser{
  float: right;
  transition: transform .2s;
}

.deleteUser:hover{
  cursor: pointer;
  transform: scale(1.2);
}

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
