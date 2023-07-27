<template>
  <v-dialog v-model="value" max-width="800" persistent fullscreen>
    <v-card max-width="800" class="rounded-lg">
      <v-toolbar color="primary" dense elevation="0">
        <v-toolbar-title class="white--text font-weight-bold">{{ promotion.name }}  - {{ promotion.type }}</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn icon @click="$emit('input', false)">
          <v-icon color="white">mdi-close</v-icon>
        </v-btn>
      </v-toolbar>
      <v-form ref="form">
        <v-card-text>
          <v-row>
            <v-col class="col-md-7 col-12">
              <v-row>
                <v-col class="col-12">
                  <graphComponent :xAxis="xAxisGraph" :yAxis="yAxisGraph"></graphComponent>
                </v-col>
              </v-row>
            </v-col>
            <v-col class="col-md-5 col-12">
              <v-row>
                <v-col class="col-12">
                  <GeneralCardComponent>
                    <v-img :src="`https://batumi.descuentosya.uy${promotion.main_picture.url}`"></v-img>
                  </GeneralCardComponent>
                </v-col>
                <v-col class="col-12">
                  <GeneralCardComponent>
                    <GeneralCardTitleComponent>Clientes</GeneralCardTitleComponent>
                    <v-data-table hide-default-footer :headers="clientHeaders" :items="clients">
                      <template v-slot:item.asociate="{item}">
                        <v-btn small rounded color="info" v-if="checkIfClientIsAsociated(item)">Asociado</v-btn>
                        <v-btn small rounded color="success" v-else @click="asociateClient(item)">Asociar</v-btn>
                      </template>
                    </v-data-table>
                  </GeneralCardComponent>
                </v-col>
                <v-col class="col-12">
                  <GeneralCardComponent>
                    <GeneralCardTitleComponent>Revalorizacion anual</GeneralCardTitleComponent>
                    <v-data-table hide-default-footer :headers="revalorizationHeaders" :items="promotion.revalorization">
                      <template v-slot:item.value="{item}">
                        {{ item.value }} %
                        </template>
                    </v-data-table>
                    <v-divider></v-divider>
                    <v-card-actions>
                      <v-row>
                        <v-col class="col-md-4 col-12">
                          <FormsFieldsSelectComponent :items="years" dense v-model="revalorization.year" label="Fecha"></FormsFieldsSelectComponent>
                        </v-col>
                        <v-col class="col-md-4 col-12">
                          <FormsFieldsTextComponent type="number" dense v-model="revalorization.value" label="Valor(%)"></FormsFieldsTextComponent>

                        </v-col>
                        <v-col class="col-md-4 col-12">
                          <v-btn color="yellow" block @click="addRevalorization()" class="rounded-lg mt-7" height="38">Guardar</v-btn>
                        </v-col>
                      </v-row>
                    </v-card-actions>
                  </GeneralCardComponent>
                </v-col>
                <v-col class="col-12" v-if="promotion.data.disponibility">
                  <GeneralCardComponent>
                    <GeneralCardTitleComponent>Disponibilidad</GeneralCardTitleComponent>
                    <v-card-subtitle class="font-weight-bold black--text">
                      Disponibilidad completa: {{ promotion.data.disponibility.complete ? 'Si' : 'No' }}
                    </v-card-subtitle>
                    <v-divider></v-divider>
                    <v-data-table hide-default-footer :headers="dispobilityHeaders" :items="promotion.data.disponibility.disponibility_dates">
                    </v-data-table>
                  </GeneralCardComponent>
                </v-col>

              </v-row>
            </v-col>

          </v-row>
        </v-card-text>
      </v-form>
    </v-card>
  </v-dialog>

</template>

<script>
  import moment from 'moment'
  export default {
    props: {
      value: {
        type: Boolean,
        default: false
      },
      promoId: {
        type: Number,
        default: () => {}
      }
    },
    data() {
      return {
        //array with years from 2010 to 2040 
        years: Array.from({
          length: 31
        }, (v, k) => k + 2010),
        revalorization:{
          year:'2023',
          value:0
        },
        clientHeaders:[
          {
            text:'Nombre',
            value:'name'
          },
          {
            text:'Apellido',
            value:'lastname'
          },
          {
            text:'Asociar',
            value:'asociate'
          },
        ],
        dispobilityHeaders:[
          {
            text:'Desde',
            value:'from'
          },
          {
            text:'Hasta',
            value:'to'
          },
        ],
        revalorizationHeaders:[
          {
            text:'Anio',
            value:'year'
          },
          {
            text:'Valor(%)',
            value:'value',
            align:'center'
          },
        ],



        promotion: {
          clients:[],
          main_picture:{
            url:''
          },
          data:{
            disponibility:{
              complete:false,
              disponibility_dates:[]
            }
          },
          revalorization:[]
        },
        clients:[]
      }
    },
    created() {
      this.getClients()
      this.getPromotion()
    },
    methods: {
      getPromotion() {
        this.$axios.get(`/promotions/${this.promoId}/?populate=*`)
          .then((response) => {
            this.promotion = response.data.data
          })
      },
      addFeature() {
        this.promotion.features.push({
          name: ''
        })
      },
      removeFeature() {
        this.promotion.features.pop()
      },
      checkIfClientIsAsociated(client){
        return this.promotion.clients.find((clientAsociated)=>{
          return clientAsociated.id === client.id
        })
      },
      asociateClient(client){
        this.promotion.clients.push(client)
        this.$axios.put(`/promotions/${this.promotion.id}`, {
            data: this.promotion
          })
      },
      addRevalorization(){
        this.promotion.revalorization.push(this.revalorization)
        this.$axios.put(`/promotions/${this.promotion.id}`, {
            data: this.promotion
          })
          this.revalorization = {
          year:'2023',
          value:0
        }


      },
      createPromotion() {
        if (this.$refs.form.validate()) {
          this.$axios.put(`/promotions/${this.promo.id}`, {
            data: this.promotion
          })
          this.$emit('promotionUpdated')
          this.$emit('input', false)

        }
      },
      getClients() {
        this.$axios.get('/users?filter[type]=client')
          .then((response)=>{
            this.clients = response.data
          })
      }
    },
    computed:{
      xAxisGraph() {
        return this.promotion.revalorization.map((revalorization)=>{
          return revalorization.year
        })
      },
      yAxisGraph() {
        return this.promotion.revalorization.map((revalorization)=>{
          return revalorization.value
        })
      }
    }
  }

</script>

<style>

</style>
