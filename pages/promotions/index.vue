<template>
  <v-container>
    <HeadersGeneralComponent>
      <template v-slot:title>
        Promociones
      </template>
      <template v-slot:subtitle>
        <v-btn color="secondary" class="rounded-lg font-weight-bold" to="/promotions/create">
          Agregar<v-icon>mdi-plus</v-icon>
        </v-btn>
      </template>
    </HeadersGeneralComponent>
    <v-row>
      <v-col class="col-12">
        <promotionsListComponent :items="items.data" @retrievePromotions="getPromotions()">
          <GeneralCardTitleComponent class="primary">
            <v-row>
              <v-col class="col-12 white--text">
                Buscar
              </v-col>
              <v-col class="col-12">
                <v-divider></v-divider>
              </v-col>
              <v-col class="col-12 col-md-3">
                <FormsFieldsTextComponent label="Nombre" v-model="search.name" label-color="white--text">
                </FormsFieldsTextComponent>
              </v-col>
              <v-col class="col-12 col-md-5">
                <FormsFieldsSelectComponent :items="['Cimentación','Construcción','Black Frame','White Frame','Amueblado']" label="Progreso" v-model=search.progress label-color="white--text">
                </FormsFieldsSelectComponent>
              </v-col>
              <v-col class="col-12 col-md-3">
                <v-btn block color="secondary" class="mt-8 rounded-lg" @click="getPromotions" height="44">
                  <v-icon>mdi-magnify</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </GeneralCardTitleComponent>
        </promotionsListComponent>
      </v-col>
    </v-row>
    <promotionsCreateComponent @promotionCreated="getPromotions()" v-model="modalCreatePromo">
    </promotionsCreateComponent>
  </v-container>
</template>


<script>
  import qs from 'qs'
  export default {
    data() {
      return {
        modalCreatePromo: false,
        modalUpdatePromo: false,
        items: [],
        search: {
          name: '',
          progress: 'Cimentación'
        }
      }
    },
    created() {
      this.getPromotions()
    },
    methods: {
      getPromotions() {
        this.items = {}
        this.$axios.get('/promotions/?populate=*', {
          params: {
            filters: {
              name: {
                $contains: this.search.name
              },
              progress: {
                $contains: this.search.progress
              }
            }

          },
          paramsSerializer: params => {
        return qs.stringify(params, {
          arrayFormat: 'brackets'
        })
      }

        }).then((response) => {
          this.items = response.data
        })
      }
    }
  }

</script>
