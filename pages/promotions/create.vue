<template>
  <v-container>
    <v-row>
      <v-col class="col-12">
        <GeneralCardComponent>
          <GeneralCardTitleComponent>Nueva promocion</GeneralCardTitleComponent>
          <v-divider></v-divider>
          <v-form ref="form">
            <v-card-text>
              <v-row>
                <v-col class="col-12 col-md-9">
                  <v-row>
                    <v-col class="col-12">
                      <FormsFieldsTextComponent v-model="promotion.name" required label="Nombre">
                      </FormsFieldsTextComponent>
                    </v-col>
                    <v-col class="col-12">
                      <GeneralCardComponent outlined>
                        <GeneralCardTitleComponent>Valor</GeneralCardTitleComponent>
                        <v-divider></v-divider>
                        <v-card-text>
                          <v-row>
                            <v-col class="col-12 col-md-6">
                              <FormsFieldsTextComponent type="number" required v-model="promotion.price" label="Valor (USD)">
                              </FormsFieldsTextComponent>
                            </v-col>
                            <v-col class="col-12 col-md-6">
                              <FormsFieldsTextComponent type="number" v-model="promotion.revalorization"
                                label="Revalorizacion (Opcional)">
                              </FormsFieldsTextComponent>
                            </v-col>
                          </v-row>
                        </v-card-text>
                      </GeneralCardComponent>
                    </v-col>


                    <v-col class="col-12">
                      <FormsFieldsTextareaComponent v-model="promotion.description" required label="Descripcion">
                      </FormsFieldsTextareaComponent>
                    </v-col>
                    <v-col class="col-12">
                      <FormsFieldsTextComponent v-model="promotion.camera_url"
                        label="Url a camaras en la obra (Opcional)">
                      </FormsFieldsTextComponent>
                    </v-col>

                    <v-col class="col-12 col-md-6">
                      <FormsFieldsTextComponent v-model="promotion.start_date" required label="Fecha de inicio"
                        type="date">
                      </FormsFieldsTextComponent>
                    </v-col>
                    <v-col class="col-12 col-md-6">
                      <FormsFieldsTextComponent v-model="promotion.end_date" required label="Fecha de fin" type="date">
                      </FormsFieldsTextComponent>
                    </v-col>
                    <v-col class="col-12">
                      <FormsFieldsSelectComponent label="Tipo" v-model="promotion.type"
                        :items="['Alquiler','Inversion','Venta']">
                      </FormsFieldsSelectComponent>
                    </v-col>
                    <v-col class="col-12" v-if="promotion.type!='Venta'">
                      <FormsFieldsSelectComponent multiple chips :items="clients" item-text="name" item-value="id"
                        v-model="promotion.clients" label="Cliente(s) asociado(s) (opcional)">
                      </FormsFieldsSelectComponent>
                    </v-col>

                    <v-col class="col-12">
                      <FormsFieldsSelectComponent label="Progreso" v-model="promotion.progress" :items="progressItems">
                      </FormsFieldsSelectComponent>
                    </v-col>

                    <v-col class="col-12">
                      <GeneralCardComponent outlined>
                        <GeneralCardTitleComponent>Caracteristicas</GeneralCardTitleComponent>
                        <v-divider></v-divider>
                        <v-card-subtitle>
                          <v-btn-toggle class="elevation-3">
                            <v-btn color="primary" small @click="removeFeature()">
                              <v-icon color="white">mdi-minus</v-icon>
                            </v-btn>
                            <v-btn color="primary" small @click="addFeature()">
                              <v-icon color="white">mdi-plus</v-icon>
                            </v-btn>
                          </v-btn-toggle>
                        </v-card-subtitle>
                        <v-card-text>
                          <v-row v-for="(feature,index) in promotion.features" :key="index">
                            <v-col class="col-12 col-md-6">
                              <FormsFieldsTextComponent v-model="feature.name" label="Nombre">
                              </FormsFieldsTextComponent>
                            </v-col>
                            <v-col class="col-12 col-md-6">
                              <FormsFieldsTextComponent v-model="feature.value" label="Valor">
                              </FormsFieldsTextComponent>
                            </v-col>
                          </v-row>
                        </v-card-text>
                      </GeneralCardComponent>
                    </v-col>
                    <v-col class="col-12 col-md-12">
                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                    <v-card-title class="text-subtitle-1 font-weight-regular">
                      Contratos
                    </v-card-title>
                    <v-divider></v-divider>
                    <GeneralUploadFilesComponent v-model="file"></GeneralUploadFilesComponent>
                  </GeneralCardComponent>
                </v-col>
                  </v-row>
                </v-col>
                <v-col class="col-12 col-md-3">
                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                    <v-card-title class="text-subtitle-1 font-weight-regular">
                      IMAGEN PRINCIPAL
                    </v-card-title>
                    <v-divider></v-divider>
                    <GeneralUploadCardComponent v-model="main_picture"></GeneralUploadCardComponent>
                  </GeneralCardComponent>
                </v-col>

              </v-row>
            </v-card-text>
          </v-form>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" @click="createPromotion()" class="rounded-lg">Guardar</v-btn>
          </v-card-actions>
        </GeneralCardComponent>
      </v-col>
    </v-row>
    <v-snackbar color="success" v-model="successSnackbar">
      Exito al crear la promocion
    </v-snackbar>

  </v-container>
</template>
<script>
  import moment from 'moment'
  export default {
    props: {
      value: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        file: [],
        main_picture: null,
        promotion: {
          name: '',
          description: '',
          start_date: '',
          end_date: '',
          progress: 'Cimentación',
          type: 'Alquiler',
          features: []
        },
        successSnackbar:false,
        progressItems: [{
            text: 'Cimentación',
            value: 'Cimentación'
          },
          {
            text: 'Construcción',
            value: 'Construcción'
          },
          {
            text: 'Black Frame',
            value: 'Black Frame'
          },
          {
            text: 'White Frame',
            value: 'White Frame'
          },
          {
            text: 'Amueblado',
            value: 'Amueblado'
          },
          {
            text: 'Finalización',
            value: 'Finalización'
          },
        ],
        clients: []
      }
    },
    created() {
      this.promotion.start_date = moment().format('YYYY-MM-DD')
      this.promotion.end_date = moment().add(7, 'days').format('YYYY-MM-DD')
      this.getClients()
    },
    methods: {
      addFeature() {
        this.promotion.features.push({
          name: ''
        })
      },
      removeFeature() {
        this.promotion.features.pop()
      },
      createPromotion() {
        if (this.$refs.form.validate()) {
          this.$axios.post('/promotions', {
            data: this.promotion
          }).then((data) => {
            if (this.file) {
              this.asociateFileToPromotion(data.data.data)
            }
            if(this.main_picture){
                this.asociateMainPictureToPromotion(data.data.data)
            }
            this.successEditSnackbar = true;
        })

        }
      },
      //asociate file to promotion in strapi 
      async asociateFileToPromotion(promotion) {
        let formData = new FormData()
        formData.append('files', this.file[0])
        formData.append('ref', 'api::promotion.promotion')
        formData.append('refId', promotion.id)
        formData.append('field', 'file')
        await this.$axios.post('/upload', formData)
      },
      async asociateMainPictureToPromotion(promotion) {
        let formData = new FormData()
        formData.append('files', this.main_picture)
        formData.append('ref', 'api::promotion.promotion')
        formData.append('refId', promotion.id)
        formData.append('field', 'main_picture')
        await this.$axios.post('/upload', formData)
      },
      getClients() {
        this.$axios.get('/users?filter[type]=client')
          .then((response) => {
            this.clients = response.data.map((r) => {
              return {
                name: r.name + " " + r.lastname,
                id: response.data.id
              }
            })
          })
      }

    }
  }

</script>

<style>

</style>
