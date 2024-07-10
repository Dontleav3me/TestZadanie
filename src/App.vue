<template>
  <v-app>
    <v-container>
      <h1 class="my-4">Каталог автомобилей</h1>
      <v-row>
        <v-col>
          <select v-model="sortKey" @change="sortCars">
            <option value="year">Год выпуска</option>
            <option value="price">Стоимость</option>
          </select>
        </v-col>
      </v-row>
      <v-row v-if="loading">
        <v-col>Загрузка...</v-col>
      </v-row>
      <v-row v-else>
        <v-col v-for="car in sortedCars" :key="car.id" cols="12" md="6" lg="4">
          <CarCard :car="car" @edit="editCar" @delete="deleteCar" />
        </v-col>
      </v-row>
      <v-dialog v-model="showModal" max-width="500">
        <v-card>
          <v-card-title>Редактировать автомобиль</v-card-title>
          <v-card-text v-if="editingCar">
            <v-text-field v-model="editingCar.name" label="Марка"></v-text-field>
            <v-text-field v-model="editingCar.model" label="Модель"></v-text-field>
            <v-text-field v-model="editingCar.price" label="Цена" type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" @click="saveEdit">Сохранить</v-btn>
            <v-btn @click="cancelEdit">Отмена</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-container>
  </v-app>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';
import CarCard from './components/CarCard.vue';

export default {
  name: 'App',
  components: {
    CarCard
  },
  setup() {
    const cars = ref([]);
    const loading = ref(true);
    const sortKey = ref('year');
    const showModal = ref(false);
    const editingCar = ref(null);

    const fetchCars = async () => {
      try {
        const response = await axios.get('https://test.tspb.su/test-task/vehicles');
        cars.value = response.data;
      } catch (error) {
        console.error('Ошибка при получении данных:', error);
      } finally {
        loading.value = false;
      }
    };

    const sortedCars = computed(() => {
      return cars.value.slice().sort((a, b) => {
        if (sortKey.value === 'year') {
          return b.year - a.year;
        } else if (sortKey.value === 'price') {
          return b.price - a.price;
        }
      });
    });

    const editCar = (car) => {
      editingCar.value = { ...car };
      showModal.value = true;
    };

    const deleteCar = (id) => {
      cars.value = cars.value.filter(car => car.id !== id);
    };

    const saveEdit = () => {
      const index = cars.value.findIndex(car => car.id === editingCar.value.id);
      if (index !== -1) {
        cars.value.splice(index, 1, editingCar.value);
        showModal.value = false;
        editingCar.value = null;
      }
    };

    const cancelEdit = () => {
      showModal.value = false;
      editingCar.value = null;
    };

    onMounted(() => {
      fetchCars();
    });

    return {
      cars,
      loading,
      sortKey,
      showModal,
      editingCar,
      sortedCars,
      fetchCars,
      editCar,
      deleteCar,
      saveEdit,
      cancelEdit
    };
  }
}
</script>

<style>
.v-application {
  font-family: 'Roboto', sans-serif;
}
</style>
