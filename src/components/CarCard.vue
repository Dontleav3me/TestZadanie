<template>
  <v-card class="mb-3">
    <v-card-title>{{ car.name }} {{ car.model }}</v-card-title>
    <v-card-subtitle>Год: {{ car.year }}</v-card-subtitle>
    <v-card-subtitle>Цена: ${{ car.price }}</v-card-subtitle>
    <v-card-subtitle>Цвет: {{ car.color }}</v-card-subtitle>
    <v-card-actions>
      <v-btn color="primary" @click="editCar">Редактировать</v-btn>
      <v-btn color="error" @click="deleteCar">Удалить</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: 'CarCard',
  props: {
    car: {
      type: Object,
      required: true
    }
  },
  methods: {
    editCar() {
      this.$emit('edit', this.car);
    },
    deleteCar() {
      this.$emit('delete', this.car.id);
    }
  }
}
</script>

<style scoped>
.mb-3 {
  margin-bottom: 1.5rem;
}
</style>
