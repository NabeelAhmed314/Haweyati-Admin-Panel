<template>
  <v-container>
    <v-row>
      <v-col
        style="display: flex ; align-items: center; justify-content: center"
        cols="12"
        md="1"
        sm="1"
      >
        <v-btn icon @click="returnBack">
          <v-icon>mdi-keyboard-backspace</v-icon>
        </v-btn>
      </v-col>
      <v-col cols="12" md="11" sm="11">
        <v-card-title
          >{{ constructionDumpster.name }} Yard Dumpster</v-card-title
        >
      </v-col>
    </v-row>
    <v-container>
      <v-card style="padding: 20px">
        <v-card-title>Information</v-card-title>
        <ImageViewer :image="constructionDumpster.image" />
        <v-text-field
          v-model="constructionDumpster.name"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Name"
          dense
        ></v-text-field>
        <v-textarea
          v-model="constructionDumpster.description"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Description"
          dense
        ></v-textarea>
      </v-card>
    </v-container>
    <v-container>
      <v-card>
        <v-card-title>Pricing</v-card-title>
        <v-data-table
          hide-default-footer
          :headers="columnsPrice"
          :items="constructionDumpster.pricing"
          fixed-header
        >
        </v-data-table>
      </v-card>
    </v-container>
    <v-container>
      <v-card>
        <v-card-title>Stores</v-card-title>
        <v-data-table
          hide-default-footer
          :headers="columnsStore"
          :items="constructionDumpster.suppliers"
          fixed-header
        >
          <template v-slot:item.person.image="{ item }">
            <v-avatar
              v-if="item.person.image != null"
              style="margin: 5px;padding: 0px"
            >
              <img
                alt="person"
                :src="
                  $axios.defaults.baseURL + 'uploads/' + item.person.image.name
                "
              />
            </v-avatar>
            <p v-if="item.person.image == null" style="margin: 0">
              No Image
            </p>
          </template>
        </v-data-table>
      </v-card>
    </v-container>
  </v-container>
</template>

<script>
import { ConstructionDumpster } from '../../../models/products/construction-dumpsters'
import ImageViewer from '../../../components/misc/image-viewer'
export default {
  name: 'DumpsterDetail',
  components: {
    ImageViewer
  },
  props: {
    constructionDumpster: {
      type: [Object, ConstructionDumpster],
      default: () => new ConstructionDumpster()
    }
  },
  data: () => ({
    columnsPrice: [
      { text: 'City', value: 'city' },
      { text: 'Rent', value: 'rent' },
      { text: 'Days', value: 'days' },
      { text: 'Extra Days Rent', value: 'extraDayRent' },
      { text: 'Helper Price', value: 'helperPrice' }
    ],
    columnsStore: [
      { text: 'Image', value: 'person.image', sortable: false },
      { text: 'Name', value: 'person.name' },
      { text: 'Email', value: 'person.email' },
      { text: 'Contact', value: 'person.contact' },
      { text: 'Address', value: 'location.address', sortable: false },
      { text: 'Services', value: 'services', sortable: false }
    ]
  }),
  methods: {
    returnBack() {
      this.$router.back()
    }
  }
}
</script>

<style scoped></style>
