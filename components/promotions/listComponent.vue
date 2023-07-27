<template>
  <generalCardComponent>
    <slot></slot>
    <v-card-text>
      <v-data-table :items-per-page="-1" :headers="headers" :items="items" hide-default-footer>
        <template v-slot:item.start_date="{ item }">
          {{ item.start_date | formatDate }}
        </template>
        <template v-slot:item.end_date="{ item }">
          {{ item.end_date | formatDate }}
        </template>
        <template v-slot:item.price="{ item }">
          US$ {{ item.price}}
        </template>

        <template v-slot:item.actions="{ item }">
          <v-btn-toggle class="elevation-3">
            <v-btn height="30" color="secondary"  small :to="`/promotions/update/${item.id}`">
              <v-icon color="white">mdi-pencil</v-icon>
            </v-btn>
            <v-btn color="secondary" @click="showPromo(item)" small height="30">
              <v-icon color="white">mdi-magnify</v-icon>
            </v-btn>
          </v-btn-toggle>


        </template>
      </v-data-table>
    </v-card-text>
    <promotionsShowComponent v-if="modalShowPromo" :promoId="promo" @promotionUpdated="$emit('retrievePromotions')"
      v-model="modalShowPromo"></promotionsShowComponent>
  </generalCardComponent>

</template>

<script>
  import moment from 'moment'
  export default {
    filters: {
      formatDate(date) {
        return moment(date).format('DD/MM/YYYY')
      }
    },
    props: {
      items: {
        type: Array,
        default: () => []
      }
    },
    data() {
      return {
        modalShowPromo: false,
        promo: {},
        headers: [{
            text: 'ID',
            value: 'id'
          },{
            text: 'Nombre',
            value: 'name'
          },
          {
            text: 'Descripción',
            value: 'description'
          },
          {
            text: 'Tipo',
            value: 'type'
          },
          {
            text: 'Valor',
            value: 'price'
          },
          {
            text: 'Fecha de inicio',
            value: 'start_date'
          },
          {
            text: 'Fecha de fin',
            value: 'end_date'
          },
          {
            text: 'Nivel de progreso',
            value: 'progress'
          },

          {
            text: 'Acciones',
            value: 'actions',
            sortable: false,
            align:'right'
          },
        ]
      }
    },
    methods: {
      showPromo(item) {
        this.promo = item.id
        this.modalShowPromo = true
      }
    }
  }
</script>

<style>

</style>