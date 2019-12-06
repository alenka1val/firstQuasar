<template>
  <div class="q-my-xl">
    <q-card>
      <q-card-title>Create new product</q-card-title>
      <q-card-main>
        <q-field :count="250">
          <q-input float-label="Name" v-model="productData.name" max-length="250"  name="name" />
        </q-field>
        <q-select v-model="productData.animal" :options="animals" float-label="Standard" name="animal" :rules="[val => !!val || 'Field is required']" />
        <q-select v-model="productData.category" :options="categories" float-label="Standard" name="category"/>
        <q-input
          filled
          type="number"
          name="price"
          v-model="productData.price"
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
            v-model="productData.description"
            :max-height="100"
            rows="7"
          />
        </q-field>
        <q-input
          filled
          type="number"
          name="onStock"
          v-model="productData.on_stock"
          float-label="On Stock"
          lazy-rules
          :rules="[
          val => val !== null && val !== '' || 'Please type your age',
          val => val > 0 && val < 100 || 'Please type a real age'
        ]"
        />
        <q-field helper="Supported format: JPG, max. file size: 300KiB" class="q-mt-lg">
          <q-uploader float-label="Images" multiple extensions=".jpg" auto-expand url="http://127.0.0.1:8000/products/upload" hide-upload-button hide-upload-progress  @add = "file_selected"/>
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
      productData: {
        name: '',
        description: '',
        animal: null,
        category: null,
        price: null,
        on_stock: '',
        file: '',
        check_if_document_upload: false,
        fd: ''
      },
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
      ]
    }
  },
  methods: {
    file_selected (file) {
      this.productData.file = file[0]
      this.productData.check_if_document_upload = true
    },
    createProduct () {
      if (this.productData.name === '') {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Name'
        })
      }
      if (this.productData.description === '') {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Description'
        })
      }
      if (this.productData.animal === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please choose Animal'
        })
      }
      if (this.productData.category === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please choose Category'
        })
      }
      if (this.productData.price === '' || this.productPrice < 0.01) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter Product Price'
        })
      }
      if (this.productData.on_stock === null) {
        this.$q.notify({
          type: 'negative', timeout: 2000, message: 'Please enter number of products on stock'
        })
      }
      /* var options = {
                  showLeafArrayIndexes: true,
                  includeNullValues: false,
                  mapping: function (value) {
                    if (typeof value === 'boolean') {
                      return +value ? '1' : '0'
                    }
                    return value
                  }
                }
                var formData = new FormData() */
      this.productData.fd = new FormData()
      this.productData.fd.append('file', this.productData.file, this.productData.file.name)
      console.log(this.productData)
      axios.post('http://127.0.0.1:8000/products/upload', this.productData.fd)
        .then(function (response) {
          if (response.data.ok) {
          }
        })
      console.log(this.productData.fd)
      var formData = new FormData()
      formData.append('file', this.productData.file, this.productData.file.name)
      formData.append('name', this.productData.name)
      axios
        .post(`http://127.0.0.1:8000/products`, formData)
        .then(response => {
          this.$q.notify({
            type: 'positive', timeout: 2000, message: 'The product has been created.'
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
      this.productData.productName.validate()
    }
  }
}
</script>

<style scoped>

</style>
