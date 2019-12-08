<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title>Edit {{ productName }}</q-card-title>
      <q-card-main>
        <q-field :count="250">
          <q-input float-label="Name" v-model="productName" max-length="250"  name="name"/>
        </q-field>
        <q-input
          filled
          type="number"
          name="price"
          v-model="productPrice"
          float-label="Price"
          lazy-rules
          :rules="[
          val => val !== null && val !== '' || 'Please type your age',
          val => val > 0 && val < 100 || 'Please type a real age'
        ]"
        />
        <q-field :count="5000">
          <q-input
            type="textarea"
            name="description"
            float-label="Description"
            v-model="productDescription"
            :max-height="100"
            rows="7"
          />
        </q-field>
        <q-input
          filled
          type="number"
          name="on_stock"
          v-model="productOnStock"
          float-label="On Stock"
          lazy-rules
          :rules="[
          val => val !== null && val !== '' || 'Please type your age',
          val => val > 0 && val < 100 || 'Please type a real age'
        ]"
        />
        <q-field helper="Supported format: JPG, max. file size: 300KiB" class="q-mt-lg">
          <q-uploader float-label="Images" extensions=".jpg" auto-expand url="http://127.0.0.1:8000/products/upload" hide-upload-button hide-upload-progress @add = "file_selected"/>
        </q-field>
      </q-card-main>
      <q-card-actions class="q-mt-md">
        <div class="row justify-end full-width docs-btn">
          <q-btn label="Cancel" flat to="/products/index"/>
          <q-btn label="Update" color="primary" @click="updateProduct"/>
        </div>
      </q-card-actions>
    </q-card>
  </div>
</template>
<style lang="stylus">
  .docs-btn .q-btn{
    padding: 15px 20px
  }
</style>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      productName: '',
      productDescription: '',
      productPrice: null,
      productOnStock: null,
      parametertName: null,
      parametertValue: null,
      selected_file: '',
      check_if_document_upload: false,
      model: []
    }
  },
  methods: {
    file_selected (file) {
      console.log(file)
      this.selected_file = file[0]
      this.check_if_document_upload = true
    },
    updateProduct () {
      axios
        .put(`http://127.0.0.1:8000/products/` + this.$route.params.id, this.productData)
        .then(response => {
          this.$q.notify({
            type: 'positive', timeout: 2000, message: 'The product has been updated.'
          })
          this.fd = new FormData()
          this.fd.append('file', this.selected_file, this.selected_file.name)
          axios.post('http://127.0.0.1:8000/products/' + response.data.id + '/upload', this.fd)
            .then(function (response) {
              if (response.data.ok) {
              }
            })
          this.$router.push({
            path: '/products/' + response.data.id + '/edit'
          })
        })
        .catch(error => {
          this.$q.notify({
            type: 'negative', timeout: 2000, message: 'An error has been occured.'
          })
          console.log(error)
        })
    }
  },
  mounted () {
    axios
      .get(`http://127.0.0.1:8000/products/` + this.$route.params.id + '/edit')
      .then(response => {
        this.productName = response.data.name
        this.productPrice = response.data.price
        this.productOnStock = response.data.on_stock
        this.productDescription = response.data.description
      })
      .catch(error => {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Loading product: an error has been occured.'
        })
        console.log(error)
      })
  },
  computed: {
    productData: function () {
      return { name: this.productName, price: this.productPrice, description: this.productDescription, on_stock: this.productOnStock }
    }
  }
}
</script>
