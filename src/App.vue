<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Header from "./components/Header.vue";
import Guitarra from "./components/Guitarra.vue";
import Footer from "./components/Footer.vue";

//consumiendo db con reactive
const state = reactive({
  guitars: [],
});

/****** Consumiendo db con ref ****/
const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito ,()=>{
  guardarLocalStorage()
},{
  deep:true
})

onMounted(() => {
  guitarras.value = db;
  state.guitars = db;
  guitarra.value = db[3];

  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  //Revisamos si elemento ya exite en el carrito
  const actualizarProducto = carrito.value.findIndex(
    (prod) => prod.id === guitarra.id
    
    );
    if (actualizarProducto >= 0) {
      console.log("producto ya se encuentra agregado");
      carrito.value[actualizarProducto].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const aumentarCantidad = (id) => {
  const index = carrito.value.findIndex((prod) => prod.id === id);
  if (carrito.value[index].cantidad >= 6) return;
  carrito.value[index].cantidad++;

};
const reducirCantidad = (id) => {
  const index = carrito.value.findIndex((prod) => prod.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;

};

const removeCarrito = (id) => {
  console.log(id);
  const newCarrito = carrito.value.filter((prod) => prod.id !== id);
  carrito.value = newCarrito;

};

const limpiarCarrito = () => {
  carrito.value = [];

};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @aumentar-cantidad="aumentarCantidad"
    @reducir-cantidad="reducirCantidad"
    @agregar-carrito="agregarCarrito"
    @remove-carrito="removeCarrito"
    @limpiar-carrito="limpiarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        v-bind:guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>
