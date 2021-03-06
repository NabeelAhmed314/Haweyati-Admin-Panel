<template>
  <v-container style="display: flex;justify-content: center">
    <SimpleForm
      :method="isUpdate ? 'patch' : 'post'"
      :data="formData"
      return
      endpoint="/drivers"
    >
      <template v-slot:header>
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
            <v-card-title>{{ title }}</v-card-title>
          </v-col>
        </v-row>
      </template>
      <div class="span-2">
        <v-card style="padding: 20px;margin-bottom: 30px">
          <v-card-title>Driver Information</v-card-title>
          <div style="margin-bottom: 20px">
            <ImageSelector
              v-model="imageFile"
              :image="driver.profile"
              @input="sendImage = $event"
            />
          </div>
          <v-text-field
            v-model="driver.profile.name"
            :rules="[required]"
            style="align-items: center !important;"
            outlined
            label="Driver Name"
            :value="driver.profile.name"
            dense
          ></v-text-field>
          <v-text-field
            v-model="driver.profile.contact"
            v-mask="['+### - #######', '+#### - ########']"
            style="align-items: center !important;"
            readonly
            outlined
            hint="0307 - 7355699"
            label="Driver Contact"
            :rules="[required]"
            :value="driver.profile.contact"
            dense
          ></v-text-field>
          <v-text-field
            v-model="driver.license"
            style="align-items: center !important;"
            outlined
            label="Driver License"
            :rules="[required]"
            :value="driver.license"
            dense
          ></v-text-field>
          <v-text-field
            v-model="driver.city"
            style="align-items: center !important;"
            outlined
            readonly
            label="Driver City"
            :value="driver.city"
            dense
          ></v-text-field>
        </v-card>
        <v-card style="padding: 20px;margin-bottom: 20px">
          <v-card-title>Driver Vehicle</v-card-title>
          <v-text-field
            v-model="driver.vehicle.name"
            style="align-items: center !important;"
            outlined
            label="Vehicle Name"
            :rules="[required]"
            :value="driver.vehicle.name"
            dense
          ></v-text-field>
          <v-text-field
            v-model="driver.vehicle.model"
            style="align-items: center !important;"
            outlined
            label="Vehicle Model"
            :rules="[required]"
            :value="driver.vehicle.model"
            dense
          ></v-text-field>
          <v-text-field
            v-model="driver.vehicle.identificationNo"
            style="align-items: center !important;"
            outlined
            label="Vehicle ID"
            :rules="[required]"
            :value="driver.vehicle.identificationNo"
            dense
          ></v-text-field>
          <v-autocomplete
            v-model="driver.vehicle.type"
            :value="driver.vehicle.type"
            outlined
            dense
            :rules="[required]"
            hide-details
            flat
            :items="types"
            item-text="name"
            :value-comparator="(a, b) => a && b && a._id === b._id"
            :item-value="(type) => type"
            @change="(item) => itemChanged(item)"
          >
          </v-autocomplete>
        </v-card>
      </div>
    </SimpleForm>
  </v-container>
</template>

<script>
import SimpleForm from '../../common/ui/widgets/SimpleForm'
import { Driver } from '../../models/driver'
import ImageSelector from '../misc/image-selector'
import {
  emailValidator,
  required,
  phoneValidator
} from '../../common/utils/validators'

export default {
  name: 'DriverForm',
  components: {
    SimpleForm,
    ImageSelector
  },
  props: {
    driver: {
      type: [Object, Driver],
      default: () => new Driver()
    },
    isUpdate: {
      type: Boolean,
      default: false
    },
    title: {
      type: String,
      default: 'Form'
    }
  },
  data: () => ({
    imageFile: null,
    sendImage: null,
    types: []
  }),
  mounted() {
    this.getTypes()
  },
  methods: {
    emailValidator,
    required,
    phoneValidator,
    returnBack() {
      this.$router.back()
    },
    async getTypes() {
      this.types = await this.$axios.$get('vehicle-type')
    },
    itemChanged(item) {
      console.log(item)
    },
    formData() {
      const formData = new FormData()
      let image = false
      for (const key of Object.keys(this.driver)) {
        console.log(key)
        if (key === 'profile') {
          for (const profile of Object.keys(this.driver[key])) {
            if (profile === 'image') {
              if (this.sendImage) {
                formData.append('image', this.sendImage)
                image = true
              } else {
                continue
              }
            } else if (profile === '_id') {
              formData.append('profile', this.driver[key][profile])
            } else if (profile === 'contact') {
              formData.append(
                profile,
                this.driver[key][profile].replace(/[^+0-9]/g, '')
              )
            } else {
              formData.append(profile, this.driver[key][profile])
            }
          }
        } else if (key === 'vehicle') {
          for (const vehicle of Object.keys(this.driver[key])) {
            if (vehicle === 'name') {
              formData.append('vehicleName', this.driver[key][vehicle])
            } else if (vehicle === 'type') {
              for (const type of Object.keys(this.driver[key][vehicle])) {
                if (type === 'name') {
                  formData.append(
                    'vehicleTypeName',
                    this.driver[key][vehicle][type]
                  )
                } else if (type === 'weight') {
                  formData.append(
                    'vehicleTypeWeight',
                    this.driver[key][vehicle][type]
                  )
                } else if (type === 'volume') {
                  formData.append(
                    'vehicleTypeVolume',
                    this.driver[key][vehicle][type]
                  )
                } else if (type === '_id') {
                  formData.append(
                    'vehicleTypeId',
                    this.driver[key][vehicle][type]
                  )
                } else if (type === 'image') {
                  formData.append(
                    'imageName',
                    this.driver[key][vehicle][type].path
                  )
                  formData.append(
                    'imagePath',
                    this.driver[key][vehicle][type].name
                  )
                }
              }
            } else {
              formData.append(vehicle, this.driver[key][vehicle])
            }
          }
        } else if (key === 'city') {
          continue
        } else if (key === 'location') {
          continue
        } else if (key === '__v') {
          continue
        } else {
          formData.append(key, this.driver[key])
        }
      }
      if (!image) {
        if (this.sendImage) {
          formData.append('image', this.sendImage)
        }
      }
      formData.append('isVehicleInfoChanged', true)
      formData.append('isAdmin', true)
      formData.forEach((item) => window.console.log(item))
      return formData
    },
    async formDataAxios(res) {
      await this.$axios.patch('drivers', this.driver)
    }
  }
}
</script>
