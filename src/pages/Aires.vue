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
    <div class="section text-center">
      <div class="container">
        <h2 class="title mb-2">Cotizacion</h2>
        <p class="description">Aires acondicionados</p>
        <div class="row">
          <div class="col-lg-6 mx-auto mb-4">
            <div class="col-11 my-2 mx-auto mb-4">
              <label class="my-auto text-center font-weight-bold">Tipo de sistema</label>
              <div class="d-flex justify-content-around mt-2">
                <n-radio v-model="quote.systemType" label="1">Solo enfriadora</n-radio>
                <n-radio v-model="quote.systemType" label="2">Calentadora</n-radio>
              </div>
            </div>
            <div class="col-11 my-2 mx-auto mb-4">
              <div class="row my-2">
                <div class="col-12 col-lg-6 col-md-6 my-auto">
                  <label class=" font-weight-bold my-auto">Capacidad</label>
                  <span class="description ml-2 my-auto">(Unidades)</span>
                </div>
                <div class="d-flex justify-content-around col-6 m-auto"  v-if="quote.unitType == 1">
                  <n-radio v-model="quote.capacity" class="mx-4" label="9">9</n-radio>
                  <n-radio v-model="quote.capacity" class="mx-4" label="12">12</n-radio>
                </div>
                <div class="d-flex justify-content-around col-6 m-auto" v-else>
                  <n-radio v-model="quote.capacity" label="12">12</n-radio>
                  <n-radio v-model="quote.capacity" label="18">18</n-radio>
                  <n-radio v-model="quote.capacity" label="24">24</n-radio>
                </div>
              </div>
            </div>
            <div class="col-11 my-2 mx-auto mb-4">
              <div class="row my-2">
                <div class="col-12 col-lg-6 col-md-6 my-auto">
                  <label class="text-center font-weight-bold my-auto">Voltaje</label>
                  <span class="description ml-2 my-auto">(Unidades)</span>
                </div>
                <div class="d-flex justify-content-around col-6  m-auto">
                  <n-radio v-model="quote.voltage" label="110">110</n-radio>
                  <n-radio v-model="quote.voltage" label="230">230</n-radio>
                </div>
              </div>
            </div>
            <div class="col-11 my-2 mx-auto mb-4">
              <div class="row my-2">
                <div class="col-12 col-lg-6 col-md-6 my-auto">
                  <label class="my-auto text-center font-weight-bold">Cantidad</label>
                </div>
                <div class="col-6 col-lg-4 col-md-4 m-auto">
                  <input type="number" class="form-control mt-2 mt-lg-0 mt-md-0" v-model="quote.quantity"/>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-6 text-center m-auto">       
            <label class="my-auto text-left font-weight-bold">Tipo</label>
            <div class="row">
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.unitType" label="1">De pared</n-radio>
                <img class="tipoAndamio" src="img/aireTipo1.png" alt="Aire Acondicionado Tipo 1" @click="selectTipo('1')"/>
              </div>
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.unitType" label="2">Unidad externa</n-radio>
                <img class="tipoAndamio" src="img/aireTipo2.png" alt="Aire Acondicionado Tipo 2" @click="selectTipo('2')"/>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="mt-4 col-lg-6 col-md-8 mx-auto">
              <fg-input
                class="input-lg"
                placeholder="Name"
                v-model="quote.userName"
                addon-left-icon="now-ui-icons users_circle-08"
                label="Name"
                inline
              >
              </fg-input>
          </div>
        </div>
        <div class="row">
          <div class="col-lg-6 col-md-8 mx-auto">
              <fg-input
                class="input-lg"
                placeholder="Email"
                v-model="quote.userEmail"
                addon-left-icon="now-ui-icons ui-1_email-85"
                label="Email"
                @change="validateEmail()"
                required
                inline
              >
              </fg-input>
          </div>
        </div>
        <div class="row">
          <div class="send-button mt-4 col-lg-6 col-md-8 mx-auto">
              <n-button type="primary" round block size="lg" @click="registerQuote(quote)">Cotizar</n-button>
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
import { Alert, Button, FormGroupInput, Radio, Checkbox } from '@/components';
import axios from "axios";

export default {
  name: 'landing',
  bodyClass: 'landing-page',
  components: {
    [Button.name]: Button,
    [FormGroupInput.name]: FormGroupInput,
    [Alert.name]: Alert,
    [Radio.name]: Radio,
    [Checkbox.name]: Checkbox,
  },
  data() {
    return {
      quote: {
        userEmail: "",
        userName: "",
        unitType: "1",
        systemType: "1",
        capacity: "12",
        voltage: "110",
        quantity: 1,
        done: false
      },
      validEmail: false,      
      renderComponent: true,
      successMessage : "",
      errorMessage : "",      
    };
  },
  mounted() {
    
  },
  methods: {
    selectTipo: function(id){
      this.quote.unitType = id;
    },
    registerQuote: function(quote){
      this.validateEmail()
      if(this.validEmail){
        if(quote.unitType != "" && quote.systemType != "" && quote.capacity != "" && quote.voltage != "" && quote.quantity > 0){  
          axios.post(this.$apiURL+"/airQuote", quote)
          .then((response) => {
            if(response.data){
              this.successMessage = "Te enviaremos la cotización al correo registrado"
            }else{
                this.errorMessage = "Error registrando proveedor"
            }
            this.restartFields()
          })
        }else{
          this.errorMessage = "Revisa todos los campos"
        }
      }
      this.hideNotifications()
    },
    validateEmail() {      
      const emailPattern = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      if (emailPattern.test(this.quote.userEmail)) {
          this.validEmail = true;
      } else {
          this.errorMessage = "Ingresa un correo válido"
          this.validEmail = false;
          this.hideNotifications()
      }
    },
    hideNotifications(){
      setTimeout(() => {
        this.successMessage = ""
        this.errorMessage = ""
      }, 4000);
    },
    restartFields(){
      this.quote.userEmail = ""
      this.quote.userName = ""      
      this.quote.systemType = "1"
      this.quote.capacity = "12"
      this.quote.voltage = "110"
      this.quote.quantity = 1
      this.quote.done = false
            
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

div.alert{
  position: fixed;
  bottom: 0;
  right: 0;
  z-index: 1;
}

.tipoAndamio{
  transition: transform .2s;
  max-height: 200px;
}

.tipoAndamio:hover{
  cursor: pointer;
  transform: scale(1.1);
}

</style>
