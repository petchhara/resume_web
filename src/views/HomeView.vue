<template>
  <div class="flex h-screen">
    <div class="m-auto container">
      <div class="mockup-window border border-base-300" data-theme="aqua">
        <div class="flex justify-center px-4 py-16 border-t border-base-300">
          <table class="table text-lg">
            <thead class="text-xl">
              <tr>
                <th>#</th>
                <th>Name</th>
                <th>Price</th>
                <th></th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(product, index) in products" :key="product._id">
                <th>{{ index + 1 }}</th>
                <td>{{ product.name }}</td>
                <td>{{ product.price }}</td>
                <td>
                  <RouterLink :to="{ name: 'edit', params: { id: product._id } }" class="btn btn-outline">Edit
                  </RouterLink>
                </td>
                <td>
                  <label :key="product._id" :for="`modal-${index}`" class="btn btn-outline">Delete</label>
                  <input :key="product._id" type="checkbox" :id="`modal-${index}`" class="modal-toggle" />
                  <div class="modal" data-theme="halloween">
                    <div class="modal-box">
                      <h3 class="font-bold text-lg">Do you want to delete {{ product.name }}</h3>
                      <div class="modal-action">
                        <label :for="`modal-${index}`" class="btn" @click="deleteProduct(product._id)">Yes</label>
                        <label :for="`modal-${index}`" class="btn" >No</label>
                      </div>
                    </div>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";
const products = ref([]);
onMounted(async () => {
  products.value = await getAllProduct();
});
const API_PATH = import.meta.env.VITE_API_PATH;
const getAllProduct = async () => {
  let data = [];
  data = await axios
    .get(`${API_PATH}/getallproduct`)
    .then((response) => {
      return response.data;
    });
  return data;
};
const deleteProduct = async (_id) => {
  await axios.delete(
    `${API_PATH}/delete/${_id}`).then(async (response) => {
      products.value = await getAllProduct();
    });
}
</script>
  
<style scoped></style>