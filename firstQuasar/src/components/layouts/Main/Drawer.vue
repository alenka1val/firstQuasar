<template>
  <div class="bg-grey-1 full-height q-px-md q-py-lg">
    <div class="q-subheading q-mb-sm">Products</div>
    <q-list link class="no-border">
      <q-item class="q-body-1" to="/products/index">
        <q-item-main>List</q-item-main>
      </q-item>
      <q-item class="q-body-1" to="/products/create">
        <q-item-main>Create</q-item-main>
      </q-item>
    </q-list>
    <div class="q-subheading q-mb-sm">Orders</div>
    <q-item class="q-body-1" to="/orders/index">
      <q-item-main>List</q-item-main>
    </q-item>
  </div>
</template>

<script>
export default {
  name: 'Drawer'
}
</script>

<style scoped>

</style>
