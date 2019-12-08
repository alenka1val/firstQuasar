<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title>Create new product</q-card-title>
      <q-card-main>
        <q-field :count="250">
          <q-input float-label="Name" v-model="productName" max-length="250"  name="name" />
        </q-field>
        <q-select v-model="productAnimal" :options="animals" float-label="Standard" name="animal" :rules="[val => !!val || 'Field is required']" />
        <q-select v-model="productCategory" :options="categories" float-label="Standard" name="category"/>
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
          name="onStock"
          v-model="on_stock"
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
          <q-btn label="Create" color="primary" @click="createProduct"/>
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
      productAnimal: null,
      productCategory: null,
      animals: [
        {
          label: 'Cat',
          value: 'cat'
        },
        {
          label: 'Dog',
          value: 'dog'
        },
        {
          label: 'Fish',
          value: 'fish'
        }
      ],
      categories: [
        {
          label: 'Toy',
          value: 'toy'
        },
        {
          label: 'Food',
          value: 'food'
        },
        {
          label: 'Other',
          value: 'stuff'
        }
      ],
      productPrice: null,
      on_stock: null,
      parametertName: null,
      parametertValue: null,
      model: [],
      selected_file: '',
      check_if_document_upload: false,
      fd: null
    }
  },
  methods: {
    file_selected (file) {
      console.log(file)
      this.selected_file = file[0]
      this.check_if_document_upload = true
    },
    createProduct () {
      if (this.productName === '') {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Name'
        })
      }
      if (this.productDescription === '') {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Description'
        })
      }
      if (this.productAnimal === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please choose Animal'
        })
      }
      if (this.productCategory === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please choose Category'
        })
      }
      if (this.productPrice === '' || this.productPrice < 0.01) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Price'
        })
      }
      if (this.on_stock === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter number of products on stock'
        })
      }
      axios
        .post(`http://127.0.0.1:8000/products`, this.productData)
        .then(response => {
          this.$q.notify({
            type: 'positive', timeout: 2000, message: 'The product has been created.'
          })
          console.log('http://127.0.0.1:8000/products/' + response.data.id + '/upload')
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
    },
    onSubmit () {
      this.productName.validate()
    }
  },
  computed: {
    productData: function () {
      return { name: this.productName, animal: this.productAnimal, category: this.productCategory, price: this.productPrice, description: this.productDescription, on_stock: this.on_stock }
    }
  }
}
</script>

<style scoped>

</style>
