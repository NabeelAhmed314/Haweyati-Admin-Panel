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
        <v-card-title>Order Detail</v-card-title>
      </v-col>
    </v-row>
    <v-container>
      <v-card style="padding: 20px">
        <v-card-title>Customer Information</v-card-title>
        <div
          style="display: flex;justify-content: center;align-items: center;margin-bottom: 30px"
        >
          <v-avatar size="80" color="white">
            <img
              v-if="order.customer.profile.image"
              style="object-fit: cover"
              alt="customer"
              :src="
                $axios.defaults.baseURL +
                  'uploads/' +
                  order.customer.profile.image.name
              "
            />
            <img
              v-else
              style="object-fit: cover"
              src="../../assets/images/placeholders/placeholder_person.jpg"
              alt="placeholder"
            />
          </v-avatar>
        </div>
        <v-text-field
          v-model="order.customer.profile.name"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Name"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.customer.profile.contact"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Contact"
          dense
        ></v-text-field>
      </v-card>
    </v-container>
    <v-container>
      <v-card style="padding: 20px">
        <v-card-title>Order Detail</v-card-title>
        <div v-if="order.rating" style="margin-bottom: 20px">
          <v-icon v-for="index in order.rating" :key="index" color="orange"
            >mdi-star</v-icon
          >
          <div style="display: inline-block;margin-left: -4px">
            <v-icon v-for="index in 5 - order.rating" :key="index" color="grey"
              >mdi-star</v-icon
            >
          </div>
        </div>
        <v-text-field
          v-model="order.orderNo"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Order #"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.service"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Service Type"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.items.length"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Total Items"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.paymentType"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Payment Type"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.total"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Total Bill"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.coupon"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Coupon Code"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.note"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Special Note"
          dense
        ></v-text-field>
        <v-card style="padding: 20px">
          <v-card-title>Order Items</v-card-title>
          <v-data-table
            hide-default-footer
            :headers="columns"
            :items="order.items"
            fixed-header
          >
            <template v-slot:item.item.variant="{ item }">
              <span
                v-for="(detail, i) of Object.keys(item.item.variant)"
                :key="i"
              >
                <p style="margin: 0">
                  {{ detail }}: {{ item.item.variant[detail] }}
                </p>
              </span>
            </template>
          </v-data-table>
        </v-card>
      </v-card>
    </v-container>
    <v-container>
      <v-card style="padding: 20px">
        <v-card-title>Pickup & Dropoff</v-card-title>
        <v-text-field
          v-model="order.items[0].item.pickUpLocation.address"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Pickup Address"
          dense
        >
          <template v-slot:prepend-inner>
            <v-icon color="light-green">mdi-map-marker</v-icon>
          </template>
        </v-text-field>
        <v-text-field
          v-model="order.dropoff.dropoffAddress"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Drop off Address"
          dense
        >
          <template v-slot:prepend-inner>
            <v-icon color="blue">mdi-map-marker</v-icon>
          </template>
        </v-text-field>
        <v-text-field
          v-if="order.dropoff.dropoffDate"
          :value="date(order.dropoff.dropoffDate)"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Date"
          dense
        ></v-text-field>
        <v-text-field
          v-if="order.dropoff.dropoffTime"
          v-model="order.dropoff.dropoffTime"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Time"
          dense
        ></v-text-field>
        <image-viewer-wide :image="order.image" />
        <div>
          <GoogleMapTwoMarker
            :drop-off-marker="order.dropoff.dropoffLocation"
            :pick-up-marker="order.items[0].item.pickUpLocation"
          />
        </div>
      </v-card>
    </v-container>
    <v-container v-if="order.driver">
      <v-card style="padding: 20px">
        <v-card-title>Driver Information</v-card-title>
        <div
          style="display: flex;justify-content: center;align-items: center;margin-bottom: 30px"
        >
          <v-avatar size="80" color="white">
            <img
              v-if="order.driver.profile.image"
              style="object-fit: cover"
              alt="customer"
              :src="
                $axios.defaults.baseURL +
                  'uploads/' +
                  order.driver.profile.image.name
              "
            />
            <img
              v-else
              style="object-fit: cover"
              src="../../assets/images/placeholders/placeholder_person.jpg"
              alt="placeholder"
            />
          </v-avatar>
        </div>
        <v-text-field
          v-model="order.driver.profile.name"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Name"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.driver.profile.contact"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Contact"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.driver.profile.email"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Email"
          dense
        ></v-text-field>
        <v-card-title>Vehicle Information</v-card-title>
        <v-text-field
          v-model="order.driver.vehicle.name"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Vehicle Name"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.driver.vehicle.model"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Vehicle Model"
          dense
        ></v-text-field>
        <v-text-field
          v-model="order.driver.vehicle.identificationNo"
          color="#313F53"
          outlined
          style="color: #313F53"
          readonly
          label="Vehicle Identification Number"
          dense
        ></v-text-field>
        <v-text-field
          style="align-items: center !important;"
          outlined
          label="Vehicle Type"
          readonly
          :value="order.driver.vehicle.type.name"
          dense
        ></v-text-field>
      </v-card>
    </v-container>
  </v-container>
</template>

<script>
import moment from 'moment'
import { Order } from '../../models/order'
import ImageViewerWide from '../misc/image-viewer-wide'
import GoogleMapTwoMarker from '@/components/misc/GoogleMapTwoMarker'

export default {
  name: 'DeliveryVehicleDetail',
  components: { GoogleMapTwoMarker, ImageViewerWide },
  props: {
    order: {
      type: [Object, Order],
      default: () => new Order()
    }
  },
  data: () => ({
    columns: [
      { text: 'Name', value: 'item.product.name' },
      { text: 'Quantity', value: 'item.qty' },
      { text: 'Distance', value: 'item.distance' },
      { text: 'Price / Km', value: 'item.product.deliveryCharges' },
      { text: 'Sub Total', value: 'subtotal' }
    ]
  }),
  methods: {
    returnBack() {
      this.$router.back()
    },
    date(date) {
      if (date) {
        return moment(date).format('MMM Do YYYY')
      }
    }
  }
}
</script>

<style scoped></style>
