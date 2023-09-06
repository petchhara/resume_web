<script setup>
import { onMounted, ref, watchEffect } from 'vue'
import axios from 'axios';
import { useRouter } from 'vue-router'
let name = ref(''), 
    price = ref(0), 
    routerName = ref(''),
    _id = ref('');
const router = useRouter()
onMounted(() => {
  routerName.value = router.currentRoute.value.name;
  _id.value = router.currentRoute.value.params?.id;
  if(routerName.value == "edit") {
    initEdit();
  }
});
const API_PATH = import.meta.env.VITE_API_PATH;
const initEdit = async () => {
  const { data } = await axios.get(`${API_PATH}/getproduct/${_id.value}`);
  name.value = data.data.name;
  price.value = data.data.price;
}
const submit = async () => {
    const req = {
        name:name.value, price:price.value
    };
    if(routerName.value == "create") {
      await axios.post(
          `${API_PATH}/create`, 
          req
      ).then((response) => {
        name.value = '';
        price.value = '';
      });
    } else {
      await axios.post(
          `${API_PATH}/update/${_id.value}`, 
          req
      ).then((response) => {
          router.push({name : 'home', replace: true});
      });
    }
}
</script>

<template>
  <div class="mockup-window border bg-base-300" data-theme="cupcake">
    <div class="flex justify-center px-4 py-16 bg-base-200">
        <input
          type="text"
          placeholder="Name"
          class="input input-bordered w-full max-w-md mr-5"
          v-model="name"
        />
        <input
          type="number"
          placeholder="Price"
          class="input input-bordered w-full max-w-md mr-5"
          v-model="price"
        />
        <button class="btn btn-outline btn-success" v-on:click="submit()">submit</button>
    </div>
  </div>
</template>

<style scoped></style>
