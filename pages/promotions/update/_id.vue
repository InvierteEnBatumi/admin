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
                <v-col class="col-12">
                  <FormsFieldsTextComponent v-model="promotion.name" :rules="rules.required" label="Nombre">
                  </FormsFieldsTextComponent>
                </v-col>
                <v-col class="col-12">
                  <GeneralCardComponent outlined>
                    <GeneralCardTitleComponent>Valor</GeneralCardTitleComponent>
                    <v-divider></v-divider>
                    <v-card-text>
                      <v-row>
                        <v-col class="col-12 col-md-6">
                          <FormsFieldsTextComponent type="number" :rules="rules.required" v-model="promotion.price"
                            label="Valor (USD)">
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
                  <generalCardComponent flat>
                    <v-card-title class="primary rounded-b-xl">
                      <v-tabs :vertical="$vuetify.breakpoint.smAndDown" v-model="tab" hide-slider slider-color="primary"
                        background-color="transparent" active-class="active-tab" grow>
                        <v-tab class="font-weight-bold">
                          Renta
                        </v-tab>
                        <v-tab class="font-weight-bold">
                          Inversion
                        </v-tab>

                      </v-tabs>
                    </v-card-title>
                    <v-card-text>
                      <v-tabs-items v-model="tab">
                        <v-tab-item class="pb-1 pt-1">
                          <GeneralCardComponent outlined v-if="tab==0">
                            <v-card-text>
                              <v-row>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent v-model="rent.owner" label="Propietario">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" :rules="rules.required"
                                    v-model="rent.rent_price" label="Precio de renta">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="text" v-model="rent.promotion" label="Promocion">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" :rules="rules.required"
                                    v-model="rent.square_meters" label="Metros cuadrados">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" :rules="rules.required"
                                    v-model="rent.estimated_annual_return" label="Retorno anual estimado(%) ">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" v-model="rent.amortization_time"
                                    label="Tiempo de amortizacion (En meses)">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsSelectComponent type="text" :items="['Diario','Mensual','Anual']"
                                    :rules="rules.required" v-model="rent.rent_type" label="Tipo de renta">
                                  </FormsFieldsSelectComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="date" :rules="rules.required"
                                    v-model="rent.purchase_date" label="Fecha de adquisicion">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="date" :rules="rules.required"
                                    v-model="rent.start_of_management" label="Inicio de gestion">
                                  </FormsFieldsTextComponent>
                                </v-col>
                              </v-row>
                              <v-row>
                                <v-col class="col-12 col-md-12">
                                  <GeneralCardComponent class="mt-0" outlined>
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Contrato
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="rentFile.contract"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>
                              </v-row>

                            </v-card-text>
                          </GeneralCardComponent>
                        </v-tab-item>
                        <v-tab-item class="pb-1 pt-1">
                          <GeneralCardComponent outlined v-if="tab==1">
                            <v-card-text>
                              <v-row>
                                <v-col class="col-12 col-md-12">
                                  <FormsFieldsTextComponent v-model="investment.owner" :rules="rules.required"
                                    label="Propietario">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" :rules="rules.required"
                                    v-model="investment.price" label="Valor de mercado (USD)">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="text" :rules="rules.required"
                                    v-model="investment.promotion" label="Promocion">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" :rules="rules.required"
                                    v-model="investment.square_meters" label="Metros cuadrados">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <FormsFieldsTextComponent type="number" v-model="investment.surplus_value_generated"
                                    :rules="rules.required" label="Capital aportado">
                                  </FormsFieldsTextComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Precontrato
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="investmentFile.precontract"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Factura de reserva
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="investmentFile.reservation_invoice"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Factura de entrada
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="investmentFile.sale_invoice"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Contrato de prestamo bancario
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="investmentFile.loan_contract"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>
                                <v-col class="col-12 col-md-6">
                                  <GeneralCardComponent class="ma-3 mt-0 rounded-t-0">
                                    <v-card-title class="text-subtitle-1 font-weight-regular">
                                      Registro de propiedad
                                    </v-card-title>
                                    <v-divider></v-divider>
                                    <GeneralUploadSingleFileComponent v-model="investmentFile.property_registration"
                                      @delete="deleteFile()">
                                    </GeneralUploadSingleFileComponent>
                                  </GeneralCardComponent>
                                </v-col>

                              </v-row>

                            </v-card-text>
                          </GeneralCardComponent>
                        </v-tab-item>

                      </v-tabs-items>
                    </v-card-text>
                  </generalCardComponent>
                </v-col>


                <v-col class="col-12">
                  <FormsFieldsTextareaComponent v-model="promotion.description" :rules="rules.required"
                    label="Descripcion">
                  </FormsFieldsTextareaComponent>
                </v-col>
                <v-col class="col-12">
                  <FormsFieldsTextComponent v-model="promotion.camera_url" label="Url a camaras en la obra (Opcional)">
                  </FormsFieldsTextComponent>
                </v-col>

                <v-col class="col-12 col-md-6">
                  <FormsFieldsTextComponent v-model="promotion.start_date" :rules="rules.required"
                    label="Fecha de inicio" type="date">
                  </FormsFieldsTextComponent>
                </v-col>
                <v-col class="col-12 col-md-6">
                  <FormsFieldsTextComponent v-model="promotion.end_date" :rules="rules.required" label="Fecha de fin"
                    type="date">
                  </FormsFieldsTextComponent>
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
                      IMAGEN PRINCIPAL
                    </v-card-title>
                    <v-divider></v-divider>
                    <GeneralUploadFilesComponent v-model="pictures"></GeneralUploadFilesComponent>
                  </GeneralCardComponent>
                </v-col>
              </v-row>
            </v-card-text>
          </v-form>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" @click="updatePromotion()" class="rounded-lg">Guardar</v-btn>
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
  import moment from 'moment';

  export default {
    props: {
      value: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        tab: 0,
        files: [],

        pictures: [],
        investment: {
          owner: '',
          price: '',
          promotion: '',
          square_meters: 0,
          contributed_capital: 0,
          surplus_value_generated: 0,
          outstanding_capital: 0,
        },
        investmentFile: {
          precontract: null,
          reservation_invoice: null,
          sale_invoice: null,
          loan_contract: null,
          property_registration: null,
        },
        rent: {
          owner: '',
          rent_price: '',
          promotion: '',
          square_meters: 0,
          contributed_capital: 0,
          surplus_value_generated: 0,
          outstanding_capital: 0,
          start_of_management: '',
          rent_type: 'Diario'
        },
        rentFile: {
          contract: null,
        },
        rules: {
          required: [(v) => !!v || 'Este campo es requerido']
        },
        files: [],
        promotion: {
          name: '',
          description: '',
          start_date: '',
          end_date: '',
          progress: 'Cimentación',
          type: 'Renta',
          features: [],
        },
        successSnackbar: false,
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
      };
    },
    created() {
      this.getPromotionData();
      this.getClients();
    },
    methods: {
      getPromotionData() {
        const promotionId = this.$route.params.id; // Reemplaza con el ID correcto de la promoción
        this.$axios
          .get(`/promotions/${promotionId}/?populate=*`)
          .then((response) => {
            this.promotion = response.data.data;
            if (this.promotion.pictures) {
              this.pictures = this.promotion.pictures
            }
            return response.data.data
          }).then((data) => {
            console.log(data)
            if (data.type == 'Renta') {
              this.getRent()
            } else {
              this.getInvestment()
              this.tab = 1
            }
          })


          .catch((error) => {
            console.error('Error al obtener la información de la promoción:', error);
          });
      },
      deleteMainPicture() {
        this.promotion.main_picture = null;
      },
      deleteFile() {
        this.promotion.file = null;
      },
      addFeature() {
        this.promotion.features.push({
          name: ''
        })
      },
      removeFeature() {
        this.promotion.features.pop()
      },


      updatePromotion() {
        if (this.$refs.form.validate()) {
          const promotionId = this.$route.params.id; // Reemplaza con el ID correcto de la promoción
          this.$axios
            .put(`/promotions/${promotionId}`, {
              data: this.promotion
            })
            .then(async (data) => {
              this.associateMainPictureToPromotion(promotionId);
              await this.updateRentOrInvestment()
              this.successEditSnackbar = true;
              this.$router.go(-1)
            })
            .catch((error) => {
              console.error('Error al actualizar la promoción:', error);
            });
        }
      },

      getInvestment() {
        this.$axios.get(`/investments/${this.promotion.data.id}/?populate=*`).then((response) => {
          this.investment = response.data.data;
          this.investmentFile = response.data.data;
        });
      },
      getRent() {
        this.$axios.get(`/rents/${this.promotion.data.id}/?populate=*`).then((response) => {
          this.rent = response.data.data;
        });
      },

      async asociateFile(data, model, file, field) {

        let formData = new FormData()
        formData.append('files', file)
        formData.append('ref', `api::${model}.${model}`)
        formData.append('refId', data.id)
        formData.append('field', field)
        await this.$axios.post('/upload', formData)

      },
      updateRentOrInvestment() {
        if (this.promotion.type == 'Inversion') {
          this.$axios.put('/investments/' + this.investment.id, {
            data: this.investment
          }).then((data) => {
            if (this.investmentFile.precontract instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.precontract, 'precontract')
            }
            if (this.investmentFile.reservation_invoice instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.reservation_invoice,
                'reservation_invoice')
            }
            if (this.investmentFile.sale_invoice instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.sale_invoice, 'sale_invoice')
            }
            if (this.investmentFile.contract instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.contract, 'contract')
            }
            if (this.investmentFile.loan_contract instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.loan_contract, 'precontract')
            }
            if (this.investmentFile.property_registration instanceof File) {
              this.asociateFile(data.data.data, 'investment', this.investmentFile.property_registration,
                'property_registration')
            }
          })
        } else if (this.promotion.type == 'Renta') {
          this.$axios.put('/rents/' + this.rent.id, {
            data: this.rent
          }).then(() => {
            if (this.rentFile.contract instanceof File) {
              this.asociateFile(data.data.data, 'rent', this.rentFile.contract, 'contract')
            }
          })
        }

      },
      async asociateFile(data, model, file, field) {

        let formData = new FormData()
        formData.append('files', file)
        formData.append('ref', `api::${model}.${model}`)
        formData.append('refId', data.id)
        formData.append('field', field)
        await this.$axios.post('/upload', formData)

      },


      associateMainPictureToPromotion(promotionId) {
        const formData = new FormData();
        formData.append('ref', 'api::promotion.promotion');
        formData.append('refId', promotionId);
        formData.append('field', 'pictures');
        this.pictures.forEach((picture) => {
          if (picture instanceof File) {
            formData.append(`files`, picture, picture.name);
          }
        });
        this.$axios
          .post('/upload', formData)
          .catch((error) => {
            console.error('Error al asociar la imagen principal a la promoción:', error);
          });
      },
      getClients() {
        this.$axios
          .get('/users?filter[type]=client')
          .then((response) => {
            this.clients = response.data.map((r) => {
              return {
                name: r.name + ' ' + r.lastname,
                id: r.id
              };
            });
          })
          .catch((error) => {
            console.error('Error al obtener la lista de clientes:', error);
          });
      }
    }
  };
</script>

<style>

</style>