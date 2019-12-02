<template>
  <div class="q-my-xl">
    <q-table
      :data="serverData"
      row-key="name"
      :pagination.sync="serverPagination"
      :loading="loading"
      @request="request"
      :columns="columns"
      title="List of orders"
      binary-state-sort
    >
      <q-tr slot="body" slot-scope="props" :props="props">
        <q-td key="id" :props="props">
          <span>{{ props.row.id }}</span>
        </q-td>
        <q-td key="address" :props="props">
          <span>{{ props.row.address.city }}, {{ props.row.address.address }}</span>
        </q-td>
        <q-td key="email" :props="props">
          <span>{{ props.row.user.email }}</span>
        </q-td>
        <q-td key="status" :props="props">
          <span>{{ props.row.status }}</span>
        </q-td>
        <q-td class="text-right">
          <div v-if="props.row.status == 'UPDATED'">UPDATED</div>
          <div v-else>
            <q-btn round icon="arrow_upward" @click="update(props.row.id, props.row.__index)"/>
          </div>
        </q-td>
      </q-tr>
    </q-table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      columns: [
        { name: 'id', label: 'ID', field: 'id', sortable: true, align: 'left' },
        { name: 'address', label: 'Address', field: 'address', sortable: false, align: 'left' },
        { name: 'email', label: 'Email', field: 'email', sortable: false, align: 'left' },
        { name: 'status', label: 'Status', field: 'status', sortable: false, align: 'left' },
        { name: 'actions', label: 'Actions', sortable: false, align: 'right' }
      ],
      selected: [],
      loading: false,
      serverPagination: {
        page: 1,
        rowsNumber: 10, // the number of total rows in DB
        rowsPerPage: 5,
        sortBy: 'id',
        descending: true
      },
      serverData: []
    }
  },
  methods: {
    request ({ pagination }) {
      // QTable to "loading" state
      this.loading = true
      // fetch data
      axios
        .get(`http://127.0.0.1:8000/orders/list/${pagination.page}?rowsPerPage=${pagination.rowsPerPage}&sortBy=${pagination.sortBy}&descending=${pagination.descending}`)
        .then(({ data }) => {
          // updating pagination to reflect in the UI
          this.serverPagination = pagination

          // we also set (or update) rowsNumber
          this.serverPagination.rowsNumber = data.rowsNumber

          // then we update the rows with the fetched ones
          this.serverData = data.rows

          // finally we tell QTable to exit the "loading" state
          this.loading = false
        })
        .catch(error => {
          // there's an error... do SOMETHING
          console.log(error)

          // we tell QTable to exit the "loading" state
          this.loading = false
        })
    },
    update (id, rowIndex) {
      axios
        .delete(`http://127.0.0.1:8000/orders/${id}`)
        .then(() => {
          this.serverData[rowIndex].status = 'UPDATED'
          this.$q.notify({
            type: 'positive', timeout: 2000, message: 'The order has been updated.'
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
  // once mounted, we need to trigger the initial server data fetch
    this.request({
      pagination: this.serverPagination,
      filter: this.filter
    })
  }
}
</script>

<style scoped>

</style>
