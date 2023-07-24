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
                              <FormsFieldsTextComponent type="number" v-model="promotion.price" label="Valor (USD)">
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
                    <GeneralUploadFilesComponent v-model="file" @delete="deleteFile()"></GeneralUploadFilesComponent>
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
                    <GeneralUploadCardComponent v-model="main_picture" @delete="deleteMainPicture()"></GeneralUploadCardComponent>
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
    <v-snackbar color="success" v-model="successEditSnackbar">
      Exito al editar la promocion
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
      file: [],
      main_picture: null,
      successSnackbar:false,
      promotion: {
        name: '',
        description: '',
        start_date: '',
        end_date: '',
        progress: 'Cimentación',
        type: 'Alquiler',
        features: []
      },
      progressItems: [
        { text: 'Cimentación', value: 'Cimentación' },
        // Resto de los elementos de progreso
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
          if(this.promotion.main_picture) {
            this.main_picture = this.promotion.main_picture
          }
          if(this.promotion.file) {
            this.file = [this.promotion.file]
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
    updatePromotion() {
      if (this.$refs.form.validate()) {
        const promotionId = this.$route.params.id; // Reemplaza con el ID correcto de la promoción
        this.$axios
          .put(`/promotions/${promotionId}`, { data: this.promotion })
          .then((data) => {
            if (this.file.length>0) {
              this.associateFileToPromotion(promotionId);
            }
            if (this.main_picture) {
              this.associateMainPictureToPromotion(promotionId);
            }
            this.successEditSnackbar = true;
          })
          .catch((error) => {
            console.error('Error al actualizar la promoción:', error);
          });
      }
    },
    associateFileToPromotion(promotionId) {
      const formData = new FormData();
      formData.append('files', this.file[0]);
      formData.append('ref', 'api::promotion.promotion');
      formData.append('refId', promotionId);
      formData.append('field', 'file');
      this.$axios
        .post('/upload', formData)
        .catch((error) => {
          console.error('Error al asociar el archivo a la promoción:', error);
        });
    },
    associateMainPictureToPromotion(promotionId) {
      const formData = new FormData();
      formData.append('files', this.main_picture);
      formData.append('ref', 'api::promotion.promotion');
      formData.append('refId', promotionId);
      formData.append('field', 'main_picture');
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
  