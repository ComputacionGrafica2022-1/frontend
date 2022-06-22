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
              <label class="my-auto text-center font-weight-bold">Masa de aire</label>
              <span class="description my-auto ml-2">(Toneladas)</span>
              <div class="d-flex justify-content-around mt-2">
                <n-radio v-model="quote.mass" label="12000">12000</n-radio>
                <n-radio v-model="quote.mass" label="18000">18000</n-radio>
                <n-radio v-model="quote.mass" label="24000">24000</n-radio>
              </div>
            </div>
            <div class="col-11 my-2 mx-auto" v-if="quote.type==='2'">
              <div class="row my-2">
                <div class="col-12 col-lg-8 col-md-8">
                  <label class="my-auto text-center font-weight-bold">Longitud de ductos</label>
                  <span class="description my-auto ml-2">(Metros)</span>
                </div>
                <div class="col-6 col-lg-4 col-md-4 m-auto">
                  <input type="number" class="form-control mt-2 mt-lg-0 mt-md-0" v-model="quote.length"/>
                </div>
              </div>
              <div class="row my-2">
                <div class="col-12 col-lg-8 col-md-8">
                  <label class="my-auto text-center font-weight-bold">Grosor de lámina</label>
                  <span class="description my-auto ml-2">(Milímetros)</span>
                </div>
                <div class="col-6 col-lg-4 col-md-4 m-auto">
                  <input type="number" class="form-control mt-2 mt-lg-0 mt-md-0" v-model="quote.thickness"/>
                </div>
              </div>
              <div class="row my-2">
                <div class="col-12 col-lg-8 col-md-8">
                  <label class="my-auto text-center font-weight-bold">Número de difusores</label>
                </div>
                <div class="col-6 col-lg-4 col-md-4 m-auto">
                  <input type="number" class="form-control mt-2 mt-lg-0 mt-md-0" min="0" @change="quote.diffusers=Math.floor(quote.diffusers)" v-model="quote.diffusers"/>
                </div>
              </div>
              <div class="row my-2">
                <div class="col-12 col-lg-8 col-md-8">
                  <label class="my-auto text-center font-weight-bold">Camino de ductos</label>
                  <span class="description my-auto ml-2">(dwg)</span>
                </div>
                <div class="col-6 col-lg-4 col-md-4 m-auto">
                  <input type="file" accept=".dwg" class="t-2 mt-lg-0 mt-md-0" @change="handleFileUpload( $event )"/>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-6 text-center m-auto">       
            <label class="my-auto text-left font-weight-bold">Tipo</label>
            <div class="row">
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.type" label="1">De pared</n-radio>
                <img class="tipoAndamio" src="img/aireTipo1.png" alt="Aire Acondicionado Tipo 1" @click="selectTipo('1')"/>
              </div>
              <div class="col-lg-6 col-md-6">
                <n-radio v-model="quote.type" label="2">Unidad externa</n-radio>
                <img class="tipoAndamio" src="img/aireTipo2.png" alt="Aire Acondicionado Tipo 2" @click="selectTipo('2')"/>
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
        mass: "12000",
        length: null,
        thickness: null,
        diffusers: null,
        type: '1',
        done: false
      },
      fileUploaded: '',
      validFile: false,
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
    // registerQuote: function(quote){
    //   this.validateEmail()
    //   if(this.validEmail){
    //     if(quote.mass > 0 && quote.length > 0 && quote.thickness > 0 && quote.diffusers > 0 && this.file != ''){
    //       this.uploadFile().then(() => {
    //         if(validFile){
    //           axios.post(this.$apiURL+"/airQuote", quote)
    //           .then((response) => {
    //             if(response.data){
    //               this.successMessage = "Te enviaremos la cotización al correo registrado"
    //             }else{
    //                 this.errorMessage = "Error registrando proveedor"
    //             }
    //             this.restartFields()
    //           })
    //         }else{
    //           this.errorMessage = "Carga un archivo válido"
    //         }
    //       })
    //     }else{
    //       this.errorMessage = "Revisa todos los campos"
    //     }
    //   }
    //   this.hideNotifications()
    // },
    registerQuote: function(quote){
      this.validateEmail()
      let fileExtension = ''
      if(this.validEmail){
        if((quote.type == 1 && quote.mass > 0) || (quote.type == 2 && quote.mass > 0 && quote.length > 0 && quote.thickness > 0 && quote.diffusers > 0 && this.fileUploaded != '')){
          try {
            if(quote.type == 2){
              fileExtension = this.fileUploaded.name.substring(this.fileUploaded.name.lastIndexOf('.') + 1)
            }
            if (quote.type == 2 && fileExtension != 'dwg'){
              this.errorMessage = "Archivo inválido"    
            }else{
              let formData = new FormData();
              formData.append("file", this.fileUploaded)
              formData.append("quote", JSON.stringify(quote))
              axios.post(this.$apiURL+"/airQuote", formData, { headers: { 'content-type': 'multipart/form-data'} })
              .then((response) => {
                if(response.data){
                  this.successMessage = "Te enviaremos la cotización al correo registrado"
                }else{
                    this.errorMessage = "Error registrando proveedor"
                }
                this.restartFields()
              })
            }
          } catch (error) {
            this.errorMessage = "Archivo inválido"  
          }
        }else{
          this.errorMessage = "Revisa todos los campos"
        }
      }
      this.hideNotifications()
    },
    handleFileUpload: function( event ){
      this.fileUploaded = event.target.files[0];
      // console.log(this.fileUploaded)
    },
    async uploadFile(){
      let formData = new FormData();
      formData.append("file", this.file)
      const response = await axios.post(this.$apiURL+"/uploadFile", formData);
      if(response.status == 200) {
        this.validFile = true;
      } else {
        this.validFile = false;
      }
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
      this.quote.mass = "12000"
      this.quote.length = null
      this.quote.thickness = null
      this.quote.diffusers = null
      this.file = ''
      // this.quote.type = '1',
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
