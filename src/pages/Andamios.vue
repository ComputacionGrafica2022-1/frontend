<template>
  <div>
    <div class="page-header page-header-small">
      <parallax
        class="page-header-image"
        style="background-image: url('img/andamiosLanding.jpg')"
      >
      </parallax>
      <div class="content-center">
        <div class="container">
          <h1 class="title">Andamios</h1>
          <!-- <div class="text-center">
            <a href="#pablo" class="btn btn-primary btn-icon btn-round">
              <i class="fab fa-facebook-square"></i>
            </a>
            <a href="#pablo" class="btn btn-primary btn-icon btn-round">
              <i class="fab fa-twitter"></i>
            </a>
            <a href="#pablo" class="btn btn-primary btn-icon btn-round">
              <i class="fab fa-google-plus"></i>
            </a>
          </div> -->
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
              v-model="form.nombre"
              addon-left-icon="now-ui-icons users_circle-08"
            >
            </fg-input>
            <fg-input
              class="input-lg"
              placeholder="Email..."
              v-model="form.email"
              type="email"
              addon-left-icon="now-ui-icons ui-1_email-85"
              @change="validateEmail"
            >
            </fg-input>
            <div class="send-button">
              <n-button type="primary" round block size="lg" @click="registerUser(form)"
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
        <h2 class="title">Usuarios registrados</h2>
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
  </div>
</template>
<script>
import { Button, FormGroupInput, Card } from "@/components";
import axios from "axios";

export default {
  name: "andamios",
  bodyClass: "landing-page",
  components: {
    [Button.name]: Button,
    [FormGroupInput.name]: FormGroupInput,
    [Card.name]: Card,
  },
  data() {
    return {
      form: {
        nombre: "",
        email: "",
      },
      message : "",
      validEmail: false,
      users : [],
      renderComponent: true,
    };
  },
  mounted() {
    this.getUsers();
  },
  methods: {
    registerUser: function(){
      //  axios.post(this.$apiURL+"/user", {"nombre": this.form.name,"email": this.form.email})
       if(this.form.nombre.trim() != "" && this.validEmail){
        axios.post(this.$apiURL+"/user", this.form)
        .then((response) => {
          console.log(response)
          if(response.data){
            this.message = "Usuario registrado!"
          }else{
            this.message = this.form.email + " ya está registrado"
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
      if (emailPattern.test(this.form.email)) {
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
            console.log("ALL USERS")
            console.log(this.users)
          }else{
            this.users = []
          }
        })
    },
    deleteUser: function(userId){
      axios.delete(this.$apiURL + "/user/" + userId)
        .then((response) => {
          console.log(response.data)
          this.getUsers()
          this.forceRerender()
        })
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

<style>
.deleteUser{
  float: right;
  transition: transform .2s;
}

.deleteUser:hover{
  cursor: pointer;
  transform: scale(1.2);
}

</style>
