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
        </div>
      </div>
    </div>
    <div class="section text-center" id="updateForm">
      <div class="container">
        <h2 class="title mb-2">Cotizacion</h2>
        <p class="description">Andamios</p>
        <div class="row">
          <div class="col-lg-6 m-auto">      
            <div class="row col-11 my-2">
              <label class="col-lg-4 col-md-4 col-sm-4 col-6 my-auto text-left">Base</label>
              <input type="number" class="form-control col-4 col-lg-2 col-md-2 col-sm-2 my-auto" v-model="quote.base"/>
              <span class="description col-1 h5 my-auto p-0">m</span>
            </div> 
            <div class="row col-11 my-2">
              <label class="col-lg-4 col-md-4 col-sm-4 col-6 my-auto text-left">Altura</label>
              <input type="number" class="form-control col-4 col-lg-2 col-md-2 col-sm-2 my-auto" v-model="quote.height"/>
              <span class="description col-1 h5 my-auto p-0">m</span>
            </div> 
          </div>
          <div class="col-lg-6 text-center m-auto">       
            <label class="my-auto text-left">Tipo</label>
            <div class="row">
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.type" label="1">Tipo 1</n-radio>
                <img class="tipoAndamio" src="img/andamioTipo1.jpg" alt="Andamio con ruedas"  title="Andamio con ruedas" @click="selectTipo('1')"/>
              </div>
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.type" label="2">Tipo 2</n-radio>
                <img class="tipoAndamio" src="img/andamioTipo2.jpg" alt="Andamio sin ruedas"  title="Andamio sin ruedas" @click="selectTipo('2')"/>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="mt-4 col-lg-6 col-md-8 mx-auto">
              <fg-input
                class="input-lg"
                placeholder="Email"
                v-model="quote.userEmail"
                addon-left-icon="now-ui-icons users_circle-08"
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
        base: 5.0,
        height: 5.0,
        type: '2',
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
      this.quote.type = id;
    },
    registerQuote: function(quote){
      this.validateEmail()
      if(this.validEmail){
        if(quote.base > 0 && quote.height > 0){
          axios.post(this.$apiURL+"/scaffoldQuote", quote)
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
      this.quote.base = 5.0
      this.quote.height = 5.0
      this.quote.type = '2'
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
